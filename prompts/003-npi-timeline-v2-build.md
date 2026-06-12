<objective>
Build a **new versioned file** — `./npi-timeline-framework-v2.html` — that takes the proven, config-driven rendering engine of the existing `npi-timeline-framework.html` (the master/base) and re-populates it with **the full NPI decision flow from page 3 of the Hershey's v12 slide**, where every agent has been **recharacterized into the standard AI Refinery agent library** per the crosswalk produced in prompt 002.

The result is one self-contained, presentation-ready HTML page that a consultant can open offline and walk a client through: the 8-step NPI journey on one continuous timeline, each step showing its decisions, its FROM→TO transformation, the planner governance touchpoints, the AERA service, and — for every agent — both its business name and its standard AI Refinery `agent_class` + orchestration role.

End goal: the bespoke "…Agent" names from the slide are now expressed in the vocabulary Accenture deploys as a standard (AI Refinery), with the orchestrator → super-agent → utility/tool/human-agent architecture visible in the timeline. The master file stays untouched so the two can be diffed.
</objective>

<context>
**Read these first, in order:**

1. **The crosswalk (your content source of truth):**
@/Users/vincent.wicker/Documents/area_agent_NPI/research/npi-refinery-crosswalk.md
This was produced by prompt 002. Section 5 ("Build-ready JSON") contains a `phases` array, an `agents` array, and a `touchpoints` array already shaped for the framework's data model and carrying the AI Refinery recharacterization fields (`refineryClass`, `orchestration`, `tooling`, `transformation`, `aera`). Sections 1–4 give the prose, the agent table, the orchestration tree, and any proposed new taxonomy. **Treat section 5 as the primary data; use sections 1–4 to fill descriptions, transformation notes, and orchestration detail.** If the crosswalk is missing or incomplete, STOP and report — do not invent the mapping (that is prompt 002's job).

2. **The engine to reuse (the master/base — DO NOT EDIT IT):**
@/Users/vincent.wicker/Documents/area_agent_NPI/npi-timeline-framework.html
Study its architecture closely: the `CAPABILITIES`, `NODE_TYPES`, `JOURNEY` data objects (≈ lines 311–462), the inline-SVG `GLYPHS`, and the render engine below them (the `$`, `el`, `state`, phase-bar/arrow rendering, card rendering, click-to-expand detail, capability/status filtering). You are **copying this file's engine and CSS as the foundation** for v2, then extending the data model and renderer to express the new v12 + AI Refinery content. Preserve everything that already works: single-file/zero-dependency, data-driven separation, continuous horizontal timeline, click-to-expand, filters, Accenture-purple aesthetic.

3. **The original slide (for visual fidelity reference):**
@/Users/vincent.wicker/Documents/area_agent_NPI/hersheys_npi_planning_v12.html
Page 3 — the section from `<!-- ══ S1: NPI DECISION FLOW ══ -->` (≈ line 64) to the "Build the Plan / Execute & Adjust / Learn & Scale" summary cards (≈ line 630). Use it to match the *vocabulary and visual encoding* (FROM→TO panels, ◆ decision rows, the **TRANSFORMED·AUGMENTED** / **ENABLED·AUTOMATED** badges, 🔧 tooling tags, the 👤 planner-touchpoint band, the AERA band, the PRE-LAUNCH/LAUNCH/POST-LAUNCH grouping). You are NOT copying v12's markup — you are re-rendering its content through v2's engine. Read `./CLAUDE.md` if present for conventions.
</context>

<requirements>

<data_model>
Extend the framework's data objects (do not break the existing render contract — content still lives in editable data at the top of the `<script>`, the engine still reads 100% from it):

1. **`phases`** — the 8 v12 steps in order, each with at least `{ id, label, group }` where `group` ∈ `"pre-launch" | "launch" | "post-launch"` (labels: "Build the Plan" / "Execute & Adjust" / "Learn & Scale"). Carry the step's FROM→TO and summary as phase-level fields (e.g. `from`, `to`, `summary`). Placeholder steps (e.g. step 4 Downstream Planning Systems, step 6 Distribution & Fulfillment Execution) render as visibly-empty/"Placeholder" phases — do not fabricate decisions for them.

2. **`agents`** — from the crosswalk's `agents` array. Each keeps the existing fields (`id`, `phaseId`, `title`, `type`, `status`, `description`, `capabilities`, `metric`, `optional`) and gains:
   - `refineryClass` — the AI Refinery `agent_class` (e.g. `FlowSuperAgent`, `CustomAgent`, `ToolUseAgent`, `UtilityAgent`, `HumanAgent`, `AnalyticsAgent`, `ResearchAgent`, `A2AClientAgent`).
   - `orchestration` — its role in the architecture (e.g. `{ role: "flow-super-agent", nodes:[…], edges:[…] }` for an orchestrating agent, or `{ role: "worker", parent: "<super-agent name>" }` for a utility/tool worker).
   - `tooling` — the 🔧 tags (array of strings).
   - `transformation` — `{ badge, note }` (e.g. badge `"TRANSFORMED·AUGMENTED"`).
   - `aera` — the step's AERA note string, or `null`.

3. **`touchpoints`** (new top-level array, or modeled as `type:"gate"`/`HumanAgent` agents) — the 3 planner governance gates.

4. **Taxonomy** — apply any new `NODE_TYPES`/`CAPABILITIES` entries the crosswalk's section 4 proposed (with the suggested ids/labels/colors). Add an inline-SVG glyph for any new capability so there are still zero external assets.
</data_model>

<rendering>
Extend the renderer so the new data is visible and faithful to v12 page 3:

1. **Continuous 8-step timeline, grouped.** Keep the single horizontal scrollable lane + continuous numbered arrow, but visually band the three groups (PRE-LAUNCH / LAUNCH / POST-LAUNCH) with their "Build the Plan / Execute & Adjust / Learn & Scale" labels above the relevant phase spans.
2. **FROM → TO per step.** Render each phase's FROM→TO transformation (the "manual today → AI-driven tomorrow" pair) in the phase header or a band under the stage bar, mirroring v12's two-column FROM/TO panel.
3. **Agent cards carry the recharacterization.** On each card's compact view: business title, the AI Refinery `refineryClass` as a small badge, the node-type badge, capability chips, and the transformation badge (**TRANSFORMED·AUGMENTED** etc.). In the **click-to-expand detail**: full description, `refineryClass` + plain-English meaning, the orchestration role (and if it's a `FlowSuperAgent`, list its `nodes`/`edges` as the pipeline it coordinates), the 🔧 tooling tags, the transformation note, the AERA note, status, and full capability list.
4. **Orchestration is legible.** For a `FlowSuperAgent`/`SuperAgent`, visually distinguish it (e.g. an "orchestrator" treatment) and show the worker agents it coordinates — either nested under it or linked in the expand view — so the reader sees the orchestrator → utility/tool/human-agent structure. The 3 `HumanAgent` planner touchpoints render as distinct gate markers on the timeline.
5. **AERA band.** Where a step calls the AERA Decision Cloud, surface an AERA marker/band on that phase (consistent with v12's AERA placeholder), labeled as a *called decision-intelligence service* (the AI Refinery `ToolUseAgent`/`A2AClientAgent` node), not an orchestrator.
6. **Filters extended.** Keep capability + status filters; ADD a filter by AI Refinery `refineryClass` (toggle each class on/off) so a viewer can isolate, say, all `CustomAgent`s or all `HumanAgent` gates. Keep "clear filters."
7. **Legend.** Extend the legend to cover the new `refineryClass` badges and any new node-types/capabilities.
</rendering>

<aesthetic>
Match the master's consulting-grade Accenture style (purple `#A100FF` accent, neutral grays, generous whitespace, ≤200ms transitions, system fonts). Carry v12's encoding cues where they aid recognition: TRANSFORMED·AUGMENTED vs ENABLED·AUTOMATED as differently-colored badges, ◆ decision glyph, 🔧 tooling tag styling, the purple planner-touchpoint band, the green/teal AERA band. Keep the header with title/subtitle and a swappable brand lockup (Accenture / Hershey's). The page must look ready to present to a client.
</aesthetic>

</requirements>

<constraints>
- **Single file, zero dependencies.** All CSS in `<style>`, all JS in `<script>`. No CDN, no Google Fonts, no icon library, no build step — must open via `file://` offline. WHY: it gets emailed and opened on locked-down client machines. Inline SVG/unicode for every glyph.
- **Vanilla only.** No React/Vue/jQuery/Tailwind — plain HTML/CSS/JS so any consultant can edit it.
- **Data/engine separation preserved.** A non-coder must be able to add/edit a step or agent by editing only the data objects at the top. If adding content requires touching render logic, the design has failed.
- **Do NOT edit `npi-timeline-framework.html`.** v2 is a separate, additive file. **Do NOT invent client-confidential figures** — preserve v12's metrics verbatim, mark gaps "TBD".
- **Do NOT invent the v12→Refinery mapping** — consume it from `./research/npi-refinery-crosswalk.md`. If a needed field is missing there, flag it in your summary rather than guessing.
</constraints>

<output>
Create exactly one file:
- `./npi-timeline-framework-v2.html` — the complete, self-contained v2 timeline with the v12 page-3 NPI decision flow and AI Refinery recharacterization, ready to open in a browser.
Do not create other files. The engine must be complete (no TODOs in render logic); data may carry explicit "Placeholder" steps by design.
</output>

<verification>
Before declaring complete, verify (state pass/fail for each in your summary):
1. Valid standalone HTML: opens with no console errors and no `http(s)://` asset/script/style references anywhere (fully offline).
2. Data-driven proof: adding a hypothetical agent or step to the data objects would appear on the page with no other code change; the renderer iterates the data and reads every new field (`refineryClass`, `orchestration`, `tooling`, `transformation`, `aera`).
3. All 8 v12 steps render in one continuous horizontal lane, banded into PRE-LAUNCH / LAUNCH / POST-LAUNCH; placeholder steps show as placeholders.
4. Each agent card shows its business title AND its AI Refinery `refineryClass` badge; expand reveals orchestration role (FlowSuperAgent nodes/edges where applicable), tooling, transformation note, and AERA note.
5. The 3 planner touchpoints render as `HumanAgent` gate markers; AERA renders as a called-service band, not an orchestrator.
6. Filters work for capability, status, AND `refineryClass`; "clear filters" restores all.
7. Click-to-expand is keyboard-accessible (Enter opens, Escape closes); status styling preserved (deployed solid / planned dashed).
8. Cross-check against the crosswalk: every agent and step in `./research/npi-refinery-crosswalk.md` section 5 appears in the page; no fabricated agents or numbers.
</verification>

<success_criteria>
- One self-contained `./npi-timeline-framework-v2.html` opens offline and presents the full 8-step v12 NPI decision flow on a single horizontally-scrollable, grouped timeline.
- Every agent is shown in both its business name and its standard AI Refinery `agent_class`, with orchestration (orchestrator → super-agent → utility/tool/human worker) made legible.
- FROM→TO, transformation badges, planner touchpoints, and AERA are faithfully represented; the master file is untouched.
- 100% of content derives from editable data objects; capability/status/refineryClass filters and click-to-expand all function; the result is client-presentation-ready.
</success_criteria>
