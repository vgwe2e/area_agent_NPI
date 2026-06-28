# NPI Agent Timeline — Handover Document
**Project:** Hershey's × Accenture · New Product Introduction (NPI) agent architecture  
**Primary deliverable:** `npi-timeline-framework-v2F.html`  
**Last updated:** 2026-06-28

---

## What this project is

A consulting artifact workspace that produces **self-contained, single-file HTML visualizations** of an AI-agent-powered NPI journey. The headline deliverable is an interactive timeline a consultant can open offline and present to a client executive. All files are `file://`-safe, zero external dependencies, vanilla HTML/CSS/JS only.

To preview locally:
```bash
python3 -m http.server 8731
# open http://localhost:8731/npi-timeline-framework-v2F.html
```

---

## File inventory

| File | Status | Purpose |
|---|---|---|
| `npi-timeline-framework-v2F.html` | **ACTIVE** | Current working deliverable — 8-step agentic NPI timeline with 5 true agents, Aera skills, orchestrator banner, Z-curve process flows |
| `npi-timeline-framework-v2D.html` | Reference | Last stable version before v2F — has the Z-curve reclassification (Automation / Traditional AI / GenAI / Human legend) |
| `npi-timeline-framework-v2E.html` | Reference | Standalone true-agent architecture overview (5 agents, 6-panel rationale, orchestrator banner) — useful for the "what is an agent" conversation |
| `npi-timeline-framework.html` | Base engine | Read-only master engine. Do not edit. |
| `hersheys_npi_planning_v12.html` | Source | Original Hershey's slide artifact — content source for the NPI decision flow |
| `edits/npi-true-agent-architecture.png` | Reference | Mermaid diagram of 5-agent architecture spanning 8 steps |
| `research/npi-refinery-crosswalk.md` | Reference | v12 → AI Refinery mapping. Do not re-derive — use this as source of truth |
| `prompts/` | Reference | Three-stage Claude pipeline that built the original artifacts |

---

## Client tech stack (important for copy accuracy)

| System | Role | Notes |
|---|---|---|
| **Kinaxis** | Demand planning + supply planning | Configured for existing SKUs, not NPI. NPI forecasts are one-time static uploads. |
| **SAP S/4HANA** | ERP backbone | Technically upgraded, but processes are antiquated. Executes against Kinaxis output. |
| **NPI SaaS tool** | Product mapping / candidate staging | Brand teams stage SKU candidates here — name not confirmed, refer to generically |
| **PlanX** | Commercial / promotional demand plan | Reconciled against Kinaxis statistical plan by the Demand Plan Reconciliation skill |
| **Snowflake / Databricks** | Data platform | Consolidated data lake — what agents actually read from |
| **Aera** | Decision intelligence platform | Running deterministic skills today (SCLP, Demand Sensing, Demand Plan Reconciliation) |

**Rule on spreadsheets:** Don't say "spreadsheets" as the primary tool. The systems above exist. Acknowledge that NPI-specific coordination sometimes falls back to offline tracking when those systems don't cover the step cleanly.

---

## Architecture decisions made (do not re-derive)

### 1. True agents vs. pipeline agents
The original design had 11 pipeline cards (Portfolio Optimization Agent, Market Segmentation Agent, etc.). These were replaced with **5 true reasoning agents** because the originals were sequential prompt pipelines, not agents. An agent = LLM + prompt where the code decides which tools to call at runtime based on intermediate results.

### 2. Decision domains, not process steps
The 5 agents map to **decision domains** (what question does it own?) not process steps (what does it do in sequence?). This is why steps 1+2, 3+4+5+6, and 7 and 8 collapse differently.

### 3. Steps 4, 5, 6 — no agents needed
Steps 4 (Downstream Planning Systems), 5 (Inventory Positioning), and 6 (Distribution & Fulfillment) are **Kinaxis/SAP batch execution** — the orchestrator observes, the systems run, no reasoning agent is needed. The Inventory & Deployment Agent sets the parameters; these steps execute them.

### 4. Aera skills are not agents
Demand Sensing, Demand Plan Reconciliation, and Supply Chain Loss Prevention are **running Aera deterministic skills** — they observe and produce outputs. The true agents consume those outputs and reason on top of them. Calling them agents would weaken the architecture story.

### 5. Altitude: 20,000 feet
The target audience is an **executive who understands a little technology**. No model names (XGBoost, Newsvendor, multi-echelon, MAPE). Replace with plain-English equivalents that still convey the mechanism. "Never make it a mystery as to what's really happening" — keep the how clear, lose the jargon.

---

## v2F data architecture

The file is split into a **DATA region** (edit here) and an **ENGINE region** (reads from data, do not edit unless adding a new rendering feature).

### Key data objects

**`TRUE_AGENTS`** — 5 reasoning agents with decision, agentic rationale, tools, color:
```
portfolio-intelligence  → blue    #1d4ed8  → Step 1
demand-intelligence     → green   #059669  → Step 2
inventory-deployment    → violet  #7c3aed  → Steps 3–6
exception-risk          → orange  #ea580c  → Step 7
learning-calibration    → sky     #0284c7  → Step 8
```

**`JOURNEY.phases`** — 8 steps, each with `agentId` pointing to TRUE_AGENTS, `group` (PRE-LAUNCH / LAUNCH / POST-LAUNCH), `fromTo` (before/after story), `placeholder: false` for all.

**`JOURNEY.agents`** — 8 cards total:
- 5 true agent cards (`trueAgent: true, trueAgentId: "..."`) — one per agent
- 3 Aera Skill cards (`aeraSkill: true, skillType: "deterministic"`) — Demand Sensing, Demand Plan Reconciliation, SCLP

**`JOURNEY.touchpoints`** — 3 planner governance gates (replaces the original 25+):
1. Pre-Launch Plan Approval — 3 named sign-offs (SKU shortlist, demand curve, deployment plan) before steps 4–6 execute
2. Exception Review — step 7, scored queue, approve/adjust/override
3. Post-Launch Recalibration — sell-through review, scaling/wind-down, calibration sign-off

**`FLOW_AGENTS`** — Z-curve racetrack flows. The first 5 entries (prefixed `agent-`) are the true-agent flows with `reason` type steps (◆ diamond, deep violet) marking decision/branching points. The remaining 11 entries are the original pipeline flows (kept for backward compat but not linked to current cards).

### Card types and rendering flags

| Flag | Renders as | CSS class |
|---|---|---|
| `trueAgent: true` | Business-language agent card with colored left border | `.card.true-agent` |
| `aeraSkill: true` | Lighter Aera skill card with teal badge | `.card.aera-skill` |
| *(neither)* | Original pipeline card (not used in v2F) | `.card` |

### True agent drawer sections (in order)
1. Title with colored left border
2. What it decides
3. How it works (plain English, 20k-foot)
4. What the planner approves
5. Business outcome
6. What it replaces (references Kinaxis/SAP/NPI SaaS, not spreadsheets)
7. Transformation badge + note
8. Aera skills consumed (only on Agents 2 and 4)
9. ▶ Process Flow button (links to the agent's Z-curve racetrack)

### Z-curve step types
| Type | Color | Icon | Meaning |
|---|---|---|---|
| `automation` | gray #6B7280 | ⬇ | Deterministic / batch — systems executing |
| `trad-ai` | blue #2563EB | ⚙ | Statistical / optimization models |
| `genai` | purple #A100FF | ✦ | LLM / RAG / generative reasoning |
| `human` | cyan #00A3E0 | 👤 | Planner review gate |
| `reason` | violet #7c3aed | ◆ | Agent decision point — branching or threshold judgment |

---

## UI layers (top to bottom in the viewport)

1. **Toolbar** — filters by capability, status, refineryClass
2. **Group band** — PRE-LAUNCH / LAUNCH / POST-LAUNCH colored bands
3. **Orchestrator banner** (#orchband) — purple, spans all 8 steps, click → "What is this?" panel explaining the decision graph in plain English
4. **Agent band** (#agentband) — 5 colored segments spanning consecutive phases, click → agent info panel
5. **Stage bars** (#timeline) — 8 numbered phase columns
6. **Agent cards** — true agent cards + Aera skill cards under their respective phases
7. **Touchpoint bands** — 3 governance gates spanning their phases
8. **Synthesis strip** — "25+ → 3 touchpoints" key metrics
9. **Infrastructure strip** — data sources (Kinaxis, SAP, Snowflake, POS, etc.)

### Right-side panels
- **Drawer** (`#drawer`) — opens on card click; true-agent drawers have their own branch in `openDrawer()`
- **Agent panel** (`#agentPanel`) — opens on agent band or orchestrator banner click
- **Flow overlay** (`#flowOverlay`) — full-screen Z-curve racetrack, nav arrows stay within true-agent group

---

## What was explicitly decided NOT to do

- **No agents at steps 4, 5, 6** — Kinaxis/SAP batch execution, orchestrator observes
- **No "Agentic" as a step type** in the Z-curve legend — the whole agent is agentic, not individual tool calls
- **Do not add the OSA (On-Shelf Availability) Aera skill as a card** — mentioned in step 6 `aera` field text only
- **Do not re-derive the v12 → AI Refinery mapping** — it lives in `research/npi-refinery-crosswalk.md`
- **Do not invent client-confidential figures** — use generic placeholders ("X,000 Hours/Year")

---

## Possible next session work

These were discussed but not implemented:

1. **Z-curve per true agent accessible from the agent band** — clicking an agent band segment could open the Z-curve directly, not just the info panel
2. **Dedicated Z-curve for each of the 5 agents** — currently the flow overlays exist but they show the high-level agentic steps. A deeper Z-curve showing sub-tool calls per step is a future layer
3. **Steps 4, 5, 6 visual treatment** — they're currently empty phase columns. A lightweight "Kinaxis/SAP Execution" node type or label could make the execution-vs-decision distinction visually clearer
4. **Orchestrator decision graph visual** — the `edits/npi-true-agent-architecture.png` Mermaid diagram exists; embedding or linking it from the orchestrator panel could strengthen the explainer
5. **Headcount / FTE impact band** — the synthesis strip shows "25+ → 3 touchpoints" but a band showing planner FTE reduction has been discussed in earlier sessions

---

## Engineering notes for the next session

### Playwright (for screenshots)
```javascript
const { chromium } = require('/Users/vincent.wicker/.npm-global/lib/node_modules/@playwright/cli/node_modules/playwright/index.js');
const browser = await chromium.launch({
  executablePath: process.env.HOME + '/Library/Caches/ms-playwright/chromium-1228/chrome-mac-arm64/Google Chrome for Testing.app/Contents/MacOS/Google Chrome for Testing',
  headless: true
});
```

### Mermaid CLI
```bash
/Users/vincent.wicker/.npm-global/bin/mmdc -i input.mmd -o output.png
```

### Local server
```bash
python3 -m http.server 8731 --directory /Users/vincent.wicker/Documents/area_agent_NPI
```

### Key file size
v2F is ~3,200 lines. Always read the specific section before editing. Never rewrite the whole file.
