<objective>
Add a three-level drill-down navigation flow to `npi-timeline-framework-v2G.html`. Starting from the existing Overview tab, a consultant can click into **Step 2 "Demand Forecasting"** to see an HTML recreation of the Hormel Foods agentic AI framework slide, then click **"Planning Companion"** to see an HTML recreation of the Agent-Led Demand Planning architecture — all within the same single file, maintaining the existing Accenture visual style.

The goal is to let a presenter demonstrate the full "zoom-in" story: high-level NPI command center → real client AI agent portfolio → one agent's internal architecture.
</objective>

<context>
File: `npi-timeline-framework-v2G.html` (single-file, offline, vanilla HTML/CSS/JS — no build step, no libraries).

**Current structure:**
- The file has two tabs: "Overview" (`#pane-overview`) and "Detail →" (`#pane-detail`).
- `renderOverview()` (~line 3092) builds the 8-step agent chain. Step 2 is currently a static non-interactive div with class `ov-step`.
- CSS variables: `--accent: #A100FF` (Accenture purple), `--teal: #0F9D8C`, `--ink: #1a1a2e`, `--hair: #e8e8f0`, and several others already defined.
- The file uses a `switchTab(id)` pattern to show/hide panes.

**The three navigation levels:**
1. **Level 1 — Overview (existing)**: The current `#pane-overview` tab. No changes to layout.
2. **Level 2 — Hormel Client Case**: An HTML recreation of the Hormel Foods "Built Gen-AI & Agentic Framework for Supply Chain Processes" slide (slide 4 of the Hormel Foods Agentic AI 2-pager). Shows the full agent portfolio grid organized by domain row and agent type column. The "Planning Companion" card in the Planning row must be clickable.
3. **Level 3 — Agent-Led Demand Planning**: An HTML recreation of the "Agent-Led Demand Planning" architecture diagram from the Future of Demand Planning deck. Shows the DP Orchestrator Agent coordinating five sub-agents, connected to data systems and the Demand Planner.
</context>

<content_level2>
The Hormel slide content to render faithfully as HTML:

**Title**: "Built Gen-AI & Agentic Framework for Supply Chain Processes"
**Subtitle**: "Delivering value at scale"

**Column headers** (two main columns):
- Conversational / Super Agents
- Utility Agents

**Status legend** (three states shown as colored indicator dots on cards):
- ● Agents Implemented (solid purple dot)
- ◎ Agent build in progress (outlined teal dot)
- ○ Agents in pipeline (hollow gray dot)

**Rows and agents** (row label on left, agents in the appropriate column):

| Row | Conversational/Super Agents | Utility Agents |
|-----|----------------------------|----------------|
| Planning | _(none — the ChatSCO super-agent spans all rows)_ | **Planning Companion** ✦ [build in progress] — "Gen AI-powered solution to provide factor contributions to forecasts, scenario analysis & dynamic model adjustments"; Distress Inventory Predictor [implemented] — "Gen AI agent to predict inventory at risk and suggest corrective actions to reduce inventory"; Intelligent Order Mgmt. [build in progress]; Stock Deployment Assistant [pipeline]; Cloud Data Ingest Assistant [pipeline] |
| Sourcing | | Portfolio Optimizer [implemented]; Spend Taxonomy Builder [pipeline]; Co-Man Contract Manager [pipeline]; SC Segmentation Assistant [pipeline]; Trade Value Optimizer [pipeline]; Spend Data Analytics [implemented]; Data Extractor [pipeline] |
| Manufacturing | | Mfg. Supervisor Assistant [implemented]; 3PL Invoice Auditor [implemented]; Payment Enrichment [pipeline]; Email Reader [implemented] |
| Distribution | | Transport Agent [implemented]; Language Translator [implemented] |
| Enterprise | | Presentation Generator [pipeline] |

**SupplyChain ChatSCO** spans across all rows in the Conversational/Super Agents column as the umbrella super-agent.

**Note at bottom**: "Super Agents: Understand and execute the process to mobilize utility agents to achieve the goal; Utility Agents: Execute sets of basic tasks autonomously"

**Branding**: Bottom-left shows only the Accenture ">" logo and wordmark. No client/company logo. Copyright notice: "Copyright © 2025 Accenture. All rights reserved."

**✦ = The "Planning Companion" card must be visually distinguished** (subtle highlight ring or cursor:pointer) and must fire `openDrillLevel('agent-led-demand')` on click. Tooltip or small label "Click to drill in →" on hover.
</content_level2>

<content_level3>
The Agent-Led Demand Planning architecture to render faithfully as HTML:

**Title**: "Agent-Led Demand Planning"

**Central orchestrator** (center of the diagram):
- Label: "DP Orchestrator Agent" (large, prominent — purple filled circle or rounded rect)

**Five sub-agents** arranged around the orchestrator:
1. **Data Integration & Correction Agent** — receives: "history adjustments & SKU mapping updates" from APS; outputs "harmonized & validated demand dataset" toward orchestrator
2. **Model Calibration & Monitoring Agent** — receives input from orchestrator
3. **Forecast Performance Agent** — outputs "forecasted data for KPI tracking" and "passes deviations requiring corrective action"
4. **Exception Handler** — "Reviews exceptions & prioritize actions"; interacts with Demand Planner
5. **Lifecycle Management Agent** — "Reviews new SKU adjustments & lifecycle recommendations"; interacts with Demand Planner

**Left panel — Data sources** (labeled "Data Source / System" at top, "Knowledge Layer" at bottom):
- ERP Systems
- GCP Database
- APS
- Product Master / MDM
- External Data

These feed into Agent 1 (Data Integration & Correction Agent).

**Right panel — Human interaction**:
- "Demand Planner" role (person icon or label)
- Interactions: "Business unit shares demand signals & inputs" (into DP Orchestrator); "Reviews exceptions & prioritize actions" (from Agent 4); "Reviews new SKU adjustments & lifecycle recommendations" (from Agent 5)

**Connection labels** (arrows between orchestrator and sub-agents):
- Orchestrator → Agent 1: "Triggers data extraction & harmonization"
- Agent 2 → Orchestrator (or bidirectional): "Sends forecast adjustments & RCA insights"
- Orchestrator → Agent 3: "Shares forecast adjustments & quality report"
- Agent 3 → Agent 4: "Monitors KPI & performs RCA"
- External events → Knowledge Layer arrow

**Style guidance**: Render this as a node-and-arrow diagram using pure HTML+CSS (absolutely positioned divs + SVG lines, or CSS grid with explicit placement). Use purple `#A100FF` for the orchestrator, teal `#0F9D8C` for sub-agents, and blue-gray for system nodes. Agent boxes should use rounded cards (8px radius) with subtle shadow. Arrows should be SVG `<line>` or `<path>` elements overlaid on the diagram.
</content_level3>

<requirements>

### 1. Navigation architecture

Add a minimal drill-down state machine using a JS array as a navigation stack. Do NOT touch the existing tab system — the drill-down layers sit inside `#pane-overview` as a sibling div that overlays it.

```js
// Add to ENGINE region:
let drillStack = []; // items: { id: string, label: string }

function openDrillLevel(id) {
  drillStack.push(id);
  document.getElementById('pane-overview-main').style.display = 'none';
  document.getElementById('drill-container').hidden = false;
  renderDrillLevel(id);
}

function closeDrillLevel() {
  drillStack.pop();
  if (drillStack.length === 0) {
    document.getElementById('pane-overview-main').style.display = '';
    document.getElementById('drill-container').hidden = true;
  } else {
    renderDrillLevel(drillStack[drillStack.length - 1]);
  }
}
```

Add to `#pane-overview` in the HTML (immediately after the opening tag):
```html
<div id="drill-container" hidden>
  <div id="drill-breadcrumb"></div>
  <div id="drill-content"></div>
</div>
```

Wrap the existing `<!-- rendered by renderOverview() on DOMContentLoaded -->` comment and subsequent overview content in `<div id="pane-overview-main">`.

### 2. Make Step 2 clickable in renderOverview()

In `renderOverview()`, when building the step-2 `ov-chain-item`, add:
- `cursor: pointer` and a hover lift effect on the `.ov-step` circle
- A subtle "Click to explore →" micro-label below the tally (in gray, 10px)
- After the div is inserted into the DOM, attach a click listener: `stepEl.addEventListener('click', () => openDrillLevel('hormel-case'))`

Since `renderOverview()` builds HTML strings and then sets `pane.innerHTML`, use `pane.querySelector` to find the step-2 item after render and attach the listener. Add a `data-drill="hormel-case"` attribute to the step-2 item during string construction to make it easy to find.

### 3. Breadcrumb navigation

`renderDrillLevel(id)` must update `#drill-breadcrumb` with a back trail:
- **Level 2** (id=`hormel-case`): shows `← Back to Overview  /  Hormel Foods — AI Agent Framework`
- **Level 3** (id=`agent-led-demand`): shows `← Back to Overview  /  Hormel Foods — AI Agent Framework  /  Planning Companion — Agent-Led Demand Planning`

Each "← Back to Overview" segment calls `drillStack = []; closeDrillLevel()`. Each intermediate breadcrumb segment (e.g. "Hormel Foods...") on level 3 calls `drillStack = ['hormel-case']; renderDrillLevel('hormel-case')`.

Style the breadcrumb bar: `font-size: 13px; color: var(--ink-faint); padding: 8px 0 18px; border-bottom: 1px solid var(--hair); margin-bottom: 20px;` with the back-arrow link in `color: var(--accent); cursor: pointer; text-decoration: none;` and a hover underline.

### 4. Render function

```js
function renderDrillLevel(id) {
  const el = document.getElementById('drill-content');
  if (id === 'hormel-case') {
    el.innerHTML = buildHormelSlide();
    // attach Planning Companion click listener after render
    el.querySelector('[data-drill="agent-led-demand"]')
      ?.addEventListener('click', () => openDrillLevel('agent-led-demand'));
  } else if (id === 'agent-led-demand') {
    el.innerHTML = buildAgentLedDemandSlide();
  }
  // rebuild breadcrumb
  // ...
}
```

### 5. Level 2 — `buildHormelSlide()` function

Build the agent matrix as a data-driven HTML table / CSS grid. Use `HORMEL_AGENTS` data object in the DATA region:

```js
const HORMEL_AGENTS = {
  superAgents: [
    { name: "SupplyChain ChatSCO", status: "implemented", desc: "Coordinates utility agents across all planning domains" }
  ],
  rows: [
    { domain: "Planning", agents: [
      { name: "Planning Companion", status: "in-progress", desc: "Gen AI-powered solution to provide factor contributions to forecasts, scenario analysis & dynamic model adjustments", drill: "agent-led-demand" },
      { name: "Distress Inventory Predictor", status: "implemented", desc: "Gen AI agent to predict inventory at risk and suggest corrective actions to reduce inventory" },
      { name: "Intelligent Order Mgmt.", status: "in-progress", desc: "Digitize order documents (PDF, excel, email), capture details, validate and enter orders in ERP" },
      { name: "Stock Deployment Assistant", status: "pipeline", desc: "Intelligent deployment & re-deployment of the inventory across the network" },
      { name: "Cloud Data Ingest Assistant", status: "pipeline", desc: "Enables ingestion of verified and validated data and notifies anomalies" },
    ]},
    { domain: "Sourcing", agents: [
      { name: "Portfolio Optimizer", status: "implemented", desc: "Enable identifying trends, diagnosing SKU performance, to assign action plans and tracking decisions" },
      { name: "Spend Taxonomy Builder", status: "pipeline", desc: "Update taxonomy tree, map items and upload in Oracle for enhancing spend data analysis" },
      { name: "Co-Man Contract Manager", status: "pipeline", desc: "GenAI driven clause extraction, contract authoring, health check and amendments" },
      { name: "SC Segmentation Assistant", status: "pipeline", desc: "Group customers and products with like characteristics to identify most profitable and strategic customers" },
      { name: "Trade Value Optimizer", status: "pipeline", desc: "Develop optimized promotional plans by analyzing and identifying customer level trade plans" },
      { name: "Spend Data Analytics", status: "implemented", desc: "Analyze procurement and expense data to uncover cost-saving opportunities & spending patterns" },
      { name: "Data Extractor", status: "pipeline", desc: "Extract features, data, content & knowledge" },
    ]},
    { domain: "Manufacturing", agents: [
      { name: "Mfg. Supervisor Assistant", status: "implemented", desc: "Monitor equipment performance and real-time production data to detect anomalies & predict potential failures" },
      { name: "3PL Invoice Auditor", status: "implemented", desc: "Digitize invoices, performs validation and provide visibility into any discrepancy" },
      { name: "Payment Enrichment", status: "pipeline", desc: "Extract errors in payment processing, identify backup payment documents and validate payment" },
      { name: "Email Reader", status: "implemented", desc: "Extracts information from email" },
    ]},
    { domain: "Distribution", agents: [
      { name: "Transport Agent", status: "implemented", desc: "One-solution for transport KPIs, route planning, scenario analysis and route optimization" },
      { name: "Language Translator", status: "implemented", desc: "Real time multi-language Translation / AI services for shop floor shift briefings" },
    ]},
    { domain: "Enterprise", agents: [
      { name: "Presentation Generator", status: "pipeline", desc: "Generates presentations, summaries and supporting content" },
    ]},
  ]
};
```

**Visual layout rules for the Hormel grid:**
- Two main column sections side-by-side: "Conversational / Super Agents" (left, ~30% width) and "Utility Agents" (right, ~70%)
- The Conversational column shows a single "SupplyChain ChatSCO" card that spans all rows — tall, spanning the full height of the grid
- Each domain row has a left-side row label (Planning, Sourcing, etc.) and a right-side flex-wrap of utility agent cards
- Agent card size: ~160px wide, 100px tall min, rounded corners (8px), white background, 1px `var(--hair)` border
- Agent card anatomy: top row with colored status dot + agent name (bold, 12px), body text in 10px gray
- Status dot colors: implemented = `#A100FF` solid, in-progress = `#0F9D8C` outlined (border only), pipeline = `#d0d0da` solid
- **Planning Companion card** gets `cursor: pointer`, `border-color: var(--accent)`, and a subtle `box-shadow: 0 0 0 2px #A100FF22` to signal it's interactive. Add `data-drill="agent-led-demand"` to this card.
- Slide header (title + subtitle) styled in Accenture consulting manner: title ~22px bold `var(--ink)`, subtitle 13px `var(--ink-faint)`
- Status legend bar below the header, before the grid
- Note at bottom of grid in 10px italic gray
- Accenture ">" + wordmark in bottom-left, copyright bottom-right, both 11px `var(--ink-faint)`

### 6. Level 3 — `buildAgentLedDemandSlide()` function

Render the architecture as a positioned SVG+HTML overlay diagram within a fixed-height container (~520px tall). Use a relative-positioned outer div, absolutely-positioned agent node divs, and an SVG element (100%×100%, `pointer-events:none`, `position:absolute; top:0; left:0`) for the connecting arrows.

**Node positions** (percentages of container width/height):
- Data sources panel (left): 0–18%, centered vertically
- DP Orchestrator (center): 42–58%, 38–58% vertically
- Agent 1 (Data Integration): 22–36%, 12–28%
- Agent 2 (Model Calibration): 22–36%, 38–54%
- Agent 3 (Forecast Performance): 22–36%, 64–80%
- Agent 4 (Exception Handler): 62–76%, 18–34%
- Agent 5 (Lifecycle Management): 62–76%, 64–80%
- Demand Planner (right): 82–96%, 36–54%

**Arrow connections** (SVG `<path>` with `stroke` and `marker-end` arrowhead):
- Data sources → Agent 1: gray `#9ca3af`
- Agent 1 → Orchestrator: purple `#A100FF`, label "harmonized & validated demand dataset"
- Orchestrator → Agent 1: label "Triggers data extraction & harmonization"
- Orchestrator → Agent 2: teal `#0F9D8C`, label "Sends forecast adjustments & RCA insights"
- Orchestrator → Agent 3: teal, label "Shares forecast adjustments & quality report"
- Agent 3 → Agent 4: label "Monitors KPI & performs RCA"
- Agent 3: output label "passes deviations requiring corrective action"
- Agent 3: output label "forecasted data for KPI tracking"
- Agent 4 → Demand Planner: label "Reviews exceptions & prioritize actions"
- Agent 5 → Demand Planner: label "Reviews new SKU adjustments & lifecycle recommendations"
- Demand Planner → Orchestrator: label "Business unit shares demand signals & inputs"

**Node styles:**
- Orchestrator: `background: var(--accent)`, white text, 56px height, font-weight 700
- Sub-agents (1–5): `background: #0F9D8C` (teal), white text, 48px height
- Data source boxes: `background: var(--bg)`, `border: 1px solid var(--hair)`, gray text, 32px height each
- Demand Planner: blue-gray `#2563EB`, white, 44px height, person icon (👤 unicode)
- All nodes: `border-radius: 8px`, `font-size: 11px`, `padding: 6px 10px`, `text-align: center`

**Labels panel on left**: "Data Source / System" heading above the data boxes; "Knowledge Layer" label below (smaller, `var(--ink-faint)`)

**Title**: "Agent-Led Demand Planning" in ~20px bold at top of the container

**Note**: Because of the complex SVG positioning, use percentage-based coordinates for the SVG lines calculated from the center-point of each node box. Nodes are absolutely positioned with `left: X%; top: Y%; transform: translateX(-50%)`. SVG lines connect the centers.
</requirements>

<constraints>
- Single file. All edits go into `npi-timeline-framework-v2G.html` only — no new files.
- The `HORMEL_AGENTS` and any other new data objects go in the **DATA region**, clearly labeled. Engine functions go in the **ENGINE region**.
- Maintain data/engine separation: `buildHormelSlide()` and `buildAgentLedDemandSlide()` read 100% from data objects — no hardcoded content in the render functions.
- The existing Overview tab, Detail tab, filters, drawer open/close, and flow overlays must be completely unaffected.
- No CDN links, no external images. Glyphs are unicode or inline SVG only.
- The drill-down views must be scrollable if content exceeds viewport height. Use `overflow-y: auto` on `#drill-content`.
- Do not show any Hormel Foods or other client company logo. The only brand mark is the Accenture ">" symbol + "Accenture" wordmark.
- `drillStack` state must be reset when the user switches away from the Overview tab via the existing tab bar, so returning to Overview shows the default pane (not a mid-drill state).
</constraints>

<verification>
Open the file in a browser (`python3 -m http.server 8731`) and verify:

1. **Overview tab** — Step 2 "Demand Forecasting" circle shows `cursor: pointer` on hover and a "Click to explore →" micro-label. Clicking it transitions the view (no page reload, no tab switch) to the Hormel agent framework slide.

2. **Hormel slide (Level 2)** — The breadcrumb shows "← Back to Overview / Hormel Foods — AI Agent Framework". The agent grid renders with all five domain rows visible. SupplyChain ChatSCO spans the full Conversational column. "Planning Companion" in the Planning row has a visible purple highlight/border and `cursor: pointer`. All other cards are non-interactive.

3. **Drill to Level 3** — Clicking Planning Companion transitions to the Agent-Led Demand Planning diagram. Breadcrumb now shows three segments. The DP Orchestrator node is centered and prominent in purple. Five sub-agent nodes (teal) are positioned around it. SVG arrows connect them with visible arrowheads. Data source panel is on the left, Demand Planner on the right.

4. **Back navigation** — Clicking the breadcrumb "← Back to Overview" from Level 3 returns directly to the main overview pane. Clicking "Hormel Foods — AI Agent Framework" returns to Level 2. Tab switching resets drill state correctly.

5. **No regressions** — Detail tab, filter bar, drawer open/close, flow overlays, and all other existing interactions work as before.
</verification>

<output>
Produce the edited `npi-timeline-framework-v2G.html` in-place (overwrite the same file). No new files.
</output>
