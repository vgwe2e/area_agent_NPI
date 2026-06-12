<objective>
Build a **single, self-contained HTML file** that renders a New Product Introduction (NPI) journey as **one continuous, horizontally-scrolling timeline** — replacing a static two-page PDF/PowerPoint where the journey had to be split across pages.

The artifact is a reusable **framework**, not a one-off picture: all content (phases, agents, capabilities) lives in an editable data object at the top of the file. The framework is **config-driven and currently mostly empty** — you build the rendering engine plus 2–3 sample entries that demonstrate every feature, and the user populates the real data later by editing the data object only (never the markup or logic).

End goal: a consultant/executive can open the file in any browser, scroll the full NPI journey on one page, click any node to read its detail, and filter the view by capability type or deployment status. It should look polished enough to present to a client (Accenture × Kroger / General Mills style).
</objective>

<context>
This framework generalizes two existing Accenture client artifacts. You cannot see the images, so the full design vocabulary is specified below — treat this prompt as the source of truth.

**Source artifact A — "Intelligent Automation" journey deck (2-page PDF).** Each page has:
- A row of **gray stage bars** across the top (process phases), sitting on a numbered **1–8 timeline arrow** that flows left→right and is meant to be *continuous* (page 2 is a continuation of page 1).
- **Cards below** each aligned under a stage. Each card has a row of small **capability icons**, a bold title, a paragraph description, and an **annual savings / ROI** figure (e.g. "4,500 Hours/Year", "$1.4mm OTIF Fees/Fines").
- Card styling encodes **status**: solid purple border = deployed / key; green dashed border = planned / future.
- A **legend** mapping each icon to a capability type.

**Source artifact B — "All Fits Together · Dynamic Flow" web screen.** A polished, Accenture-branded single-page app (Kroger logo + Accenture logo, top nav tabs). It shows a *Before* (human-led, grouped into phases: DESIGN → DATA → RUN → INTERPRET & ITERATE) vs *After* (agent-orchestrated) flow. In the *After* column each step is a card carrying:
- A **type badge**: TRIGGER, HUMAN, AGENT, or GATE.
- A **phase tag**: IDEATION, DATA, RUN, MODEL, OUTPUT, etc.
- A **step number** in a circle, and optional "Optional" steps.
This is the visual target for polish and for the node-type taxonomy.

**Key reframing from the user:** the gray bars are *stages of a New Product Introduction (NPI) phase timeline*. The cards below are things "that will be agents" — today some are deterministic automations, tomorrow agentic. The icons distinguish **different types of AI vs deterministic capability**. The whole point is to unify the two pages into ONE page.

Read `./CLAUDE.md` if present for project conventions before writing the file. There is no existing codebase to match — this is greenfield, a single file.
</context>

<requirements>

<data_model>
Define ONE JavaScript data object literal near the top of the `<script>`, clearly commented as "EDIT THIS — all content lives here." It MUST drive 100% of the rendered output. The engine reads it; nothing is hardcoded in markup. Shape it roughly as:

```js
const JOURNEY = {
  title: "New Product Introduction — Intelligent Automation Journey",
  subtitle: "Agent-orchestrated · one continuous timeline",
  // Ordered left→right. These render as the gray bars across the top.
  phases: [
    { id: "ideation",   label: "Ideation",            node: 1 },
    { id: "concept",    label: "Concept & Feasibility", node: 2 },
    // ... generic NPI placeholder phases (see <npi_phases>)
  ],
  // Each agent/node hangs under a phase via phaseId.
  agents: [
    {
      id: "scenario-ideation",
      phaseId: "ideation",
      title: "Scenario Ideation Agent",
      type: "agent",            // see <node_types>
      status: "deployed",        // "deployed" | "planned"
      description: "Proposes scenario set based on intent, network state, and Flow KPIs.",
      capabilities: ["genai", "predictive"],   // ids from <capabilities>
      metric: "9,000 Hours/Year",               // savings/ROI string, optional
      optional: false
    },
    // ... 2–3 sample agents covering different types/statuses/capabilities
  ]
};
```

Provide just enough sample data (3+ phases, 3+ agents) that every visual state is demonstrably exercised: at least one `deployed` and one `planned`, at least one of each node type, and at least one card showing 3+ capability icons. Leave the rest for the user to fill.
</data_model>

<npi_phases>
Use these generic NPI placeholder phases as the default `phases` (the user will rename/extend them). Map them onto a continuous numbered timeline (1..N) like the source 1–8 arrow:
1. Ideation → 2. Concept & Feasibility → 3. Development → 4. Validation & Testing → 5. Commercialization → 6. Launch → 7. In-Market Scale & Optimize
Treat the count as data-driven — the engine must handle any number of phases, not assume 7 or 8.
</npi_phases>

<node_types>
Each agent card carries a `type` that drives its badge label and accent color (mirror artifact B):
- `trigger` — entry point / intent (distinct accent)
- `human`   — human-in-the-loop step / gate review
- `agent`   — autonomous agent
- `gate`    — approval / decision gate (checkmark affordance)
- `workflow`— deterministic workflow / RPA
Render the type as a small uppercase badge on the card.
</node_types>

<capabilities>
Define a capability taxonomy (the legend) as data, each with an id, a label, and a distinct color/glyph. Render a compact legend on the page AND a row of capability chips/icons on each card. Use this set (from the source legend); represent glyphs with inline SVG or unicode — do NOT depend on external icon fonts or network assets:
- `predictive`  — "Predictive & Prescriptive Analytics"
- `ai-augment`  — "AI Augmentation for Knowledge-Intensive Process"
- `rpa`         — "RPA / Automation Intervention"
- `virtual-agent` — "Virtual Agent"
- `workflow`    — "Workflow"
- `best-in-class` — "Best in Class"
- `leapfrog`    — "Leapfrog Peers"
- `genai`       — "Generative AI"
</capabilities>

<layout_and_interaction>
1. **One continuous horizontal timeline.** A single scrollable lane: gray stage bars across the top sitting on a continuous numbered arrow (1..N), with agent cards positioned in columns/swimlanes under their phase. Horizontal scroll (and drag-to-pan / shift-wheel) replaces flipping pages. Include a sticky mini-overview or phase ruler so the user never loses their place while scrolling.
2. **Click-to-expand cards.** Cards show a compact summary (title, type badge, capability chips, metric). Clicking opens the full detail (full description, status, all capabilities spelled out) in an inline expansion or a modal/drawer. Keyboard-accessible (Enter/Escape, focus trap if modal).
3. **Filtering.** Toolbar controls to filter the visible cards by:
   - capability type (toggle each capability on/off), and
   - deployment status (deployed / planned / all).
   Filtering dims or hides non-matching cards smoothly; show an active-filter indicator and a "clear filters" affordance.
4. **Status styling.** `deployed` = solid accent border (purple family); `planned` = dashed border (green/teal family) — faithful to the source.
5. **Responsive-ish.** Must be usable on a laptop; degrade gracefully on narrow widths (the timeline stays horizontally scrollable rather than collapsing into nonsense).
</layout_and_interaction>

<aesthetic>
Aim for a clean, modern, consulting-grade look consistent with artifact B (Accenture style): generous whitespace, restrained palette anchored on Accenture purple (#A100FF) as the primary accent, neutral grays for stage bars, subtle borders/shadows, crisp typography (system font stack is fine — no external fonts). Smooth, purposeful transitions (≤200ms) for expand/filter — no gratuitous motion. Include a small header with the journey title/subtitle and a placeholder brand lockup area (text "Accenture" / client name) the user can swap.
</aesthetic>

</requirements>

<constraints>
- **Single file.** One `.html` file with all CSS in a `<style>` block and all JS in a `<script>` block. ZERO external dependencies — no CDN links, no Google Fonts, no icon libraries, no build step. It must work opened via `file://` with no network. WHY: it will be emailed and opened on locked-down client machines offline.
- **Vanilla only.** No React/Vue/jQuery/Tailwind. Plain HTML/CSS/JS so any consultant can edit it. WHY: the user chose a self-contained file specifically to avoid npm/build tooling.
- **Data/engine separation is the whole point.** A non-coder must be able to add a phase or an agent by editing only the `JOURNEY` object. If adding content requires touching rendering code, the design has failed. Render everything from data in a single init pass.
- Do not invent client-confidential figures. Use clearly generic placeholder text/metrics in samples (e.g. "X,000 Hours/Year").
- Inline SVG or unicode for all glyphs; never reference external image files.
</constraints>

<output>
Create exactly one file:
- `./npi-timeline-framework.html` — the complete, self-contained framework with sample data, ready to open in a browser.

Do not create any other files. Do not leave TODOs in the rendering engine (the *data* may be sparse by design; the *engine* must be complete).
</output>

<verification>
Before declaring complete, verify against this checklist (state pass/fail for each in your final summary):
1. Open logic check: the file is valid standalone HTML — opening it would render without console errors and with no network requests (no `http(s)://` asset/script/style references anywhere).
2. Data-driven proof: adding a hypothetical phase or agent object to `JOURNEY` would appear on the page with NO other code change. Confirm by tracing that the renderer iterates `JOURNEY.phases` and `JOURNEY.agents` and reads every field used in markup.
3. Continuous timeline: all phases render in one horizontal lane with a single continuous numbered arrow; horizontal scrolling works (not two stacked pages).
4. Click-to-expand works and is keyboard-accessible (Enter to open, Escape to close).
5. Filters work: toggling a capability and toggling status both change which cards are visible/dimmed; "clear filters" restores all.
6. Every node type, both statuses, and the full capability legend are visually represented by the sample data.
7. Status styling matches spec (deployed = solid purple-family border; planned = dashed green/teal border).
</verification>

<success_criteria>
- One self-contained `./npi-timeline-framework.html` opens offline with no errors and presents the entire NPI journey on a single horizontally-scrollable page.
- 100% of visible content derives from the editable `JOURNEY` data object; a non-coder can extend it by editing data only.
- Click-to-expand, capability/status filtering, and the capability legend all function as specified.
- The result looks presentation-ready in the Accenture × client visual style described.
</success_criteria>
