<objective>
Re-lay-out the NPI timeline deliverable `./npi-timeline-framework-v2A.html` so it presents as **one continuous left→right screen** a consultant can scroll horizontally only — never shift+scroll down. Today the page stacks a tall header, toolbar, ruler, a scrolling timeline, a data-systems strip, and a bottom legend; vertical real estate is exhausted and the user must scroll down to read the key. The redesign converts the bottom legend + data-systems strip into a **frozen left "key" pane** (the at-a-glance reference for what capabilities, agent classes, and systems are in play), collapses the header, trims the agent cards to fit one viewport, and routes all detail through the existing click-through drawer.

This is a client-facing consulting artifact opened offline on a locked-down machine. The whole point of the framework is data/engine separation and zero dependencies — preserve both. The end state: open the file, see a crisp header strip, a fixed left key, and a timeline you pan side-to-side; click any card or any key row to drill into detail in the right drawer.

The authoritative visual direction is the annotated layout in `./updates/udate_layout.pdf`. Read it first — its five callouts ("freeze the pane / left-to-right text", "collapse header", "get rid of side-bars", "move [title] to left side under logo", "make these click-through drill downs on every box") are the contract for this task.
</objective>

<context>
- **Project type**: Single self-contained HTML artifact — all CSS in one `<style>`, all JS in one `<script>`, no CDN/build/npm, must work via `file://` offline. Vanilla HTML/CSS/JS only. Read `./CLAUDE.md` for the hard constraints and aesthetic (Accenture purple `#A100FF`, neutral grays, system font, transitions ≤200ms).
- **File to edit**: `./npi-timeline-framework-v2A.html` (1710 lines). This is the v2A working copy — edit it in place. Do **not** touch `npi-timeline-framework.html` (the master base engine, kept read-only for diffing) or `npi-timeline-framework-v2.html`.
- **Data/engine separation is mandatory and already in place** — the `<script>` has two clearly-commented DATA regions (`EDIT THIS` at line ~605 and `EDIT THIS (v2A)` at line ~956) and an ENGINE region (`ENGINE — renders entirely from the data` at line ~1014). Layout changes belong in CSS + the render functions; do not hardcode content into markup, and do not break the rule that a non-coder can still add a phase/agent/source by editing only the data objects.

**Current structure you are reshaping (verified line references):**
- `header.app` (line ~546): brand (logo + HSY + client) on the left, `.titles` (`#jTitle` / `#jSub`) pushed right via `margin-left:auto`.
- `.toolbar` (line ~558): AI Refinery class filters (`#classFilters`), capability filters (`#capFilters`), filter stat, clear button. `position: sticky; top: 0`.
- `.ruler` (line ~571): phase jump pills, `position: sticky; top: 57px`.
- `.scrollhint` (line ~573): "Drag, or hold Shift + scroll, to pan →".
- `.timeline-wrap` → `.groupband` (`#groupband`) + `.timeline` (`#timeline`): the horizontally-scrolling lane. `wirePan()` (line ~1608) wires drag-to-pan, shift-wheel + vertical-wheel→horizontal, and rAF-throttled groupband lockstep via `translateX`.
- `.foundation` (`#foundation`, line ~580): the data-systems infrastructure strip, rendered by `buildFoundation()` (~1642) from `DATA_SOURCES` + `INFRA`.
- `.legend` (line ~582): the bottom legend rendered by `buildLegend()` (~1356) — capability grid (`#legendGrid`), AI Refinery classes (`#classLegend`), node types (`#typeLegend`), transformation badges (`#badgeLegend`), Aera skill type (`#skillTypeLegend`), data sources (`#sourceLegend`).
- `.drawer` (`#drawer`, line ~598): the right slide-in, opened by `openDrawer(a)` (~1440), `openTouchpointDrawer(t)` (~1562), `openSourceDrawer(sourceId)` (~1680). This is the existing click-through target — reuse it.
- Agent card built by `buildCard(a)` (~1263): toprow (node badge + rfBadge), `h3` title, `.desc` (3-line clamp narrative), `.tbadge` transformation, `.metric`, `.caps` capability icons, orch/worker hints, `.srcs` source icons.

@./CLAUDE.md
@./updates/udate_layout.pdf
@./npi-timeline-framework-v2A.html
</context>

<requirements>
Implement all five PDF callouts. Decisions already locked with the user (do not re-litigate):

**1. Frozen left "key" pane — FULL key (replaces both bottom sections).**
- Add a fixed/sticky left pane (call it `.keypane` / `#keypane`) that does NOT scroll horizontally when the timeline pans. It is the consultant's permanent reference aperture.
- Move the **entire** contents of the bottom `.legend` AND the `.foundation` data-systems strip into this left pane: capability legend, AI Refinery agent classes, node types, transformation badges, Aera skill type, and the data sources (owned systems + signals). Then **remove** the now-empty bottom `.legend` and `.foundation` blocks from the page flow.
- Text must read **normally, left-to-right, top-to-bottom** down the pane (the user's mock had it rotated/vertical — undo that). Compact section headers, then rows.
- Keep it driven by the existing render functions (`buildLegend`, `buildFoundation`) — retarget their mount points into the keypane rather than rewriting the data. The journey title ("NPI Decision Flow — recharacterized…") concept stays under the logo per callout 4, not duplicated in the key.

**2. Collapse the header → slim bar, title under logo.**
- Reduce `header.app` to one thin row. Put the Accenture logo + HSY + client on the left, and move the journey title (`#jTitle` / `#jSub`, currently right-aligned via `.titles`) to sit **under/beside the logo on the left** — matching the PDF "move to left side under logo" callout. Remove `margin-left:auto` right-alignment.
- Strip redundant/verbose toolbar labels (the `.glabel` "AI Refinery class" / "Capability" text) down to the minimum needed; keep the filter chips and clear button reachable. Merge the toolbar into the slim header zone if that buys vertical space.
- **Get rid of the side-bars** (PDF callout): remove any vertical/rotated sidebar strips on the far left that the mock flags for deletion. If they are an artifact of prior layout, delete the markup + CSS.

**3. One-screen fit — compact cards, detail in drawer.**
- Trim every agent card (`buildCard`) to: node-type badge + AI Refinery class badge (`rfBadge`), `h3` title, capability icons (`.caps`), and the transformation `.tbadge`. **Remove the 3-line `.desc` narrative, the `.metric` line, orch/worker hint text, and source-icon row from the card face** — these move into the drawer (most already render there; verify `openDrawer` shows description, metric, tooling, orchestration pipeline, and sources, and add any that are missing).
- Goal: a column that stacks 2–3 cards + a touchpoint band still fits within one viewport height alongside the phase bar, FROM→TO band, and node row. Tighten card padding, the FROM→TO `min-height`, and lane `gap` as needed. The page as a whole must not require vertical scrolling at a typical laptop height (~800–900px); the only scroll is horizontal on the timeline.

**4. Click-through drill-downs on every box — reuse the right drawer.**
- Every agent card already opens the drawer; preserve that (Enter opens, Escape closes, focus management intact).
- Additionally make the **left-key rows click-through**: clicking a capability / agent-class / node-type / badge / skill-type / data-source row opens its meaning/detail in the same right drawer. Data-source rows already have `openSourceDrawer`; add lightweight drawer openers for the taxonomy rows using the `meaning` text already in `CAPABILITIES`/`REFINERY_CLASSES`/`NODE_TYPES`/`SKILL_TYPES`. Keep these keyboard-accessible (role/button semantics + Enter).
- Trimmed card text is acceptable precisely because the drawer now carries the full narrative — confirm nothing the card used to show is lost from the drawer.

**5. Scroll model.**
- Horizontal-only pan stays the primary interaction. Keep `wirePan()` drag-to-pan and wheel→horizontal mapping. Update the `.scrollhint` copy to reflect the new model (e.g. "Drag or scroll to pan the timeline →") since vertical scrolling should no longer be necessary.
- The left key pane and the timeline must be siblings in a horizontal flex layout: key pinned left at a fixed width, timeline taking remaining width and owning the horizontal overflow. The groupband `translateX` lockstep must continue to track `#timeline.scrollLeft`.
</requirements>

<constraints>
- **Single file, zero dependencies, offline `file://`.** No CDN, no fonts, no images, no build step. Glyphs stay inline SVG/unicode.
- **Preserve data/engine separation.** All new content comes from existing data objects; add CSS + adjust render functions. A non-coder must still add a phase/agent/source by editing only the DATA regions.
- **Do not invent client-confidential figures.** Preserve existing v12 metrics and placeholders verbatim; keep "TBD"/"Placeholder" as-is.
- **Aesthetic unchanged:** Accenture purple `#A100FF` primary, neutral grays, generous whitespace, system font, transitions ≤200ms. The result must read "crisp and good size … without being overpowering" — bigger/clearer key and card titles, less dense narrative.
- **Accessibility preserved:** keyboard open/close on cards and new clickable key rows, focus-visible rings, `prefers-reduced-motion` honored, drawer `aria-modal` and inert-background behavior intact.
- Do not edit `npi-timeline-framework.html` or `npi-timeline-framework-v2.html`. Edit only `npi-timeline-framework-v2A.html`.
- Do not re-derive the v12→AI Refinery mapping; it is fixed in `research/npi-refinery-crosswalk.md`. If a field you need is missing, flag it rather than inventing it.
</constraints>

<implementation>
Thoughtfully sequence the change so the engine still renders in a single init pass:
1. **Layout shell first.** Wrap the existing content in a horizontal flex: `<div class="stage"> <aside class="keypane"> … </aside> <main class="timeline-region"> … </main> </div>`. Give the page a fixed full-viewport height (`100vh`/`100dvh`) so only the timeline scrolls horizontally and nothing forces body vertical scroll.
2. **Header/toolbar collapse** — restructure `header.app`, move titles under the logo, slim the toolbar, delete sidebar markup + CSS.
3. **Keypane mounts** — add the keypane container and retarget `buildLegend()` and `buildFoundation()` to render into it (reuse their innerHTML logic; just change the root nodes). Remove the old `.legend` and `.foundation` page blocks. Make the pane vertically scrollable internally only if its own content exceeds height — but it should not be the thing the consultant has to scroll to use the timeline.
4. **Card trim** — edit `buildCard` to drop `.desc`, `.metric`, hints, and `.srcs` from the card; verify `openDrawer` surfaces all of them. Tighten card/lane/fromto CSS for vertical fit.
5. **Clickable key rows** — add drawer openers for taxonomy rows (a small `openInfoDrawer(title, kind, meaning, color)` helper is fine; data-source rows keep `openSourceDrawer`).
6. **Scroll wiring** — confirm `wirePan` still binds to `#timeline` inside its new `.timeline-region` parent and the groupband lockstep still works.

Avoid: introducing any framework, external asset, or second file; hardcoding legend/source content into markup; removing data the drawer relies on; changing the brand palette.
</implementation>

<verification>
Before declaring complete, verify with the only pre-approved command and a browser check:
- `python3 -m http.server 8731` then open `http://localhost:8731/npi-timeline-framework-v2A.html`. Optionally capture a Playwright snapshot/console log into `.playwright-cli/`.
- At a ~1440×850 viewport: confirm the **page body does not scroll vertically** — only `#timeline` scrolls horizontally (drag, wheel, and shift-wheel all pan left↔right).
- The left key pane stays fixed in place while the timeline pans; its section headers and rows read left-to-right, top-to-bottom.
- The bottom legend and the data-systems strip no longer appear at the bottom — their content lives in the left key.
- The header is one slim row; the journey title sits under/beside the logo on the left; no rotated sidebars remain.
- Every agent card opens the right drawer (mouse + Enter); the drawer shows description, metric, tooling, orchestration pipeline, transformation, and data sources (i.e., nothing lost from the trimmed cards).
- Clicking a capability / agent-class / node-type / badge / skill-type / data-source row in the left key opens its detail in the drawer; Escape closes; focus returns.
- Filters (class + capability), clear-filters, and ruler jump-to-phase still function.
- Confirm zero external requests in the console (fully offline) and no JS errors.
</verification>

<success_criteria>
- The deliverable presents as one left→right screen: no vertical body scroll at laptop height; horizontal timeline pan is the only scroll.
- A frozen left key pane carries the full taxonomy + data-systems reference, written normally, and every row is click-through into the drawer.
- Header collapsed to a slim bar with the title moved under the logo; sidebars removed.
- Agent cards trimmed to title + badges + capability icons + transformation badge, with full detail intact in the drawer.
- Single self-contained offline HTML file; data/engine separation, accessibility, and Accenture aesthetic preserved; the two sibling framework files untouched.
</success_criteria>
