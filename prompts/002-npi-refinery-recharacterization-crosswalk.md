<objective>
Produce a **recharacterization crosswalk**: a single structured document that maps every NPI decision and named agent on **page 3 of the Hershey's NPI planning slide (v12)** onto the **standard AI Refinery agent library** — and, where a decision needs more than one agent, specifies the **orchestration** (which AI Refinery super-agent coordinates the workers) and the **utility agents** that sit underneath.

This crosswalk is the analytical backbone for a downstream build (prompt 003) that re-renders the NPI journey in `npi-timeline-framework.html`'s engine. Its consumer is another Claude instance, not a human — so it must be precise, complete, and directly liftable into a JavaScript data object. The end goal: every bespoke "…Agent" named in the slide gets re-expressed in the vocabulary Accenture uses as a standard (AI Refinery `agent_class`es), with the multi-agent architecture (orchestrator → super-agent → utility/tool/human agents) made explicit.
</objective>

<context>
This is an Accenture × Hershey's (HSY) consulting artifact. Three inputs matter:

**1. The decisions to map — page 3 of v12 ("NPI DECISION FLOW").**
@/Users/vincent.wicker/Documents/area_agent_NPI/hersheys_npi_planning_v12.html
Page 3 is the section beginning at the comment `<!-- ══ S1: NPI DECISION FLOW (like page 2) ══ -->` (around line 64) and running through the three summary cards "Build the Plan / Execute & Adjust / Learn & Scale" (around line 630). Read this section in full. It describes **8 NPI decision steps**, grouped:
- **PRE-LAUNCH (steps 1–4)** — "Build the Plan": Portfolio & Launch Planning · Demand Forecasting & Signal Conversion · Inventory Planning · Downstream Planning Systems.
- **LAUNCH (steps 5–7)** — "Execute & Adjust": Inventory Positioning & Deployment · Distribution & Fulfillment Execution · Launch Execution & In-Market Adjustments.
- **POST-LAUNCH (step 8)** — "Learn & Scale": Post-Launch Optimization & Scaling.

For each step, extract verbatim (or near-verbatim) the following where present:
- Step number, title, and group (PRE-LAUNCH / LAUNCH / POST-LAUNCH).
- The step summary paragraph.
- The **FROM → TO** transformation (the "From: manual…" / "To: AI-driven…" pair).
- Each **◆ key decision** line.
- The **named agent(s)** that drive each decision (e.g. *Portfolio Optimization Agent*, *Market Segmentation Agent*, *NPI Forecast Agent*, *Signal Capture Agent*, *Demand Sensing Agent*, *Inventory Optimization and Planning Agent*, *Inventory deployment and replenishing agent*, *Performance Monitoring Agent*, *Scaling Agent*, *Learning Loop Agent*, *Lifecycle Management Agent*, *Exception Handler Agent*).
- The **🔧 tooling tags** attached to a decision (e.g. *MILP · weighted scoring · like-product regression · Monte Carlo*; *ML clustering · segmentation models*).
- The **transformation badge** on each decision (e.g. **TRANSFORMED · AUGMENTED**, **ENABLED · AUTOMATED**) and its one-line explanation.
- The **👤 Planner touchpoint** (the human-in-the-loop gate) for the step.
- The **AERA** placeholder block for the step.
- Steps that are explicitly "Placeholder" (e.g. step 4 Downstream Planning Systems, step 6 Distribution & Fulfillment Execution) — record them as placeholders, do not invent content.

Supporting context (read for the orchestration vocabulary only — do NOT pull their content into the per-step crosswalk): the same file's "Future Agentic NPI" control-tower section (the **TO-BE · Agentic NPI Orchestration — Planner as Launch Control Tower**, the *25+ → 3 touchpoints* framing, around lines 778–803) and the **Agentic Demand Planning Flows** / **AERA Decision Cloud** stack (around lines 870–945). These establish that: a planner acts as a control tower over 3 governance touchpoints, agents are orchestrated in a governed loop, and **Aera is a *called* decision-intelligence service** (returns forecast/inventory/optimization answers on request — "Skills · Cortex"). Use this to inform how AERA maps to the AI Refinery library.

**2. The standard library to map ONTO — AI Refinery.**
@/Users/vincent.wicker/Documents/Refinery/demo-blueprint.md
@/Users/vincent.wicker/Documents/Refinery/research/03-distiller-service.md
The AI Refinery (Distiller) agent library defines these standard `agent_class`es — use ONLY these; do not invent new class names:
- **Utility/worker agents:** `UtilityAgent` (text generation/summarization/narrative, customizable via `magic_prompt`, optional `self_reflection`), `ResearchAgent` (RAG / web + vector search with citations), `AnalyticsAgent` (structured-data / CSV / DB analysis), `ToolUseAgent` (function-calling against business APIs via `custom_tools` JSON schemas), `CustomAgent` (user-defined Python logic — e.g. simulators, optimizers, scorers), `HumanAgent` (human-in-the-loop approve/reject/modify gate), `A2AClientAgent` (agent-to-agent / external-platform call), `AlchemistAgent`, `DeepResearchAgent`.
- **Orchestrators / super-agents:** `SuperAgent` (dynamically decomposes a query across an agent pool), `FlowSuperAgent` (deterministic DAG with explicit `nodes` + `edges`), and the top-level **orchestrator** (router with an `agent_list`).
- **Declaration shape:** every agent is declared with `agent_class`, `agent_name`, `agent_description`, optional `magic_prompt`, optional `config` (e.g. `custom_tools`, `self_reflection`). `FlowSuperAgent` adds `nodes` + `edges`; `SuperAgent` adds an `agent_list`/`agent_pool`. Study the complete supply-chain example in demo-blueprint.md (the `DemandSurgePipeline` FlowSuperAgent with DataFetcher → SurgeSimulator → OptionScorer → FinancialAnalyst → SurgeRecommendationWriter → HumanGate) — that DAG-of-utility-agents-with-a-human-gate is the canonical pattern to imitate.

**3. The downstream consumer's data model — for shape compatibility only.**
@/Users/vincent.wicker/Documents/area_agent_NPI/npi-timeline-framework.html
Skim the `CAPABILITIES`, `NODE_TYPES`, and `JOURNEY` objects near the top of the `<script>` (≈ lines 311–462). Your crosswalk's JSON should be shaped so prompt 003 can fold it into that `JOURNEY` model with minimal transformation. Do NOT modify this file.
</context>

<analysis_requirements>
Thoroughly analyze each of the 8 steps. Consider multiple valid recharacterizations before committing to one, and justify the choice. For EACH named v12 agent, decide:

1. **Primary AI Refinery `agent_class`.** Which single standard class best captures what the bespoke agent does? Reason from the agent's actual job:
   - Optimization / simulation / scoring / regression engines (MILP, Monte Carlo, like-product regression, ML clustering) → almost always `CustomAgent` (deterministic Python) and/or `AnalyticsAgent`.
   - Anything that pulls live data or calls a business/planning system or Aera → `ToolUseAgent` (or `A2AClientAgent` when the call crosses to an external agent platform).
   - Narrative / recommendation / explainability write-ups → `UtilityAgent` with a `magic_prompt` (note anti-hallucination + evidence-grounding rules, mirroring demo-blueprint's `SurgeRecommendationWriter`).
   - Knowledge retrieval over a like-product library or external research → `ResearchAgent`.
   - Every "Planner touchpoint" → `HumanAgent` gate.
2. **Decomposition where one named agent really is several.** Many v12 "Agents" are actually a *pipeline*: fetch data → run model → score options → value them → write recommendation → human-approve. When so, recharacterize the named agent as a **`FlowSuperAgent`** orchestrating an explicit ordered list of utility/tool/custom agents (give each a concrete `agent_name`, `agent_class`, one-line `agent_description`), and define the `nodes` + `edges`.
3. **Top-level orchestration.** Define how the 8 steps compose. Map the v12 "Planner as Launch Control Tower / governed loop" onto AI Refinery: a top-level **orchestrator** (router) over the per-step super-agents, plus a `SuperAgent` where a step must handle compound/unpredictable queries (e.g. the post-launch monitoring/exception loop). State the 3 planner governance touchpoints as the `HumanAgent` gates in this architecture.
4. **AERA mapping.** Recharacterize the AERA Decision Cloud as a *called service* in AI Refinery terms (a `ToolUseAgent`/`A2AClientAgent` that returns forecast/inventory/optimization answers on request), invoked as a node inside the relevant step pipelines — not as an orchestrator. Keep AERA-placeholder steps as placeholders.
5. **Capability + node-type tagging.** For each recharacterized agent, assign the existing framework `capabilities` ids (`predictive`, `ai-augment`, `rpa`, `virtual-agent`, `workflow`, `best-in-class`, `leapfrog`, `genai`) and a `NODE_TYPES` id (`trigger` | `human` | `agent` | `gate` | `workflow`) that best fits, so prompt 003 can render it. Where the AI Refinery class implies a new node-type or capability the framework lacks, flag it explicitly with a proposed id + label rather than forcing a bad fit.

Do NOT invent client-confidential numbers. Preserve v12's own metrics/figures verbatim where they exist; mark anything absent as TBD.
</analysis_requirements>

<output_format>
Write ONE file: `./research/npi-refinery-crosswalk.md`. (Create the `./research/` directory if needed.) Structure it exactly as:

1. **`## 1. Source extraction`** — a table of all 8 steps with: number, title, group, FROM→TO, and a bullet list of each ◆ decision (decision text · named agent(s) · tooling tags · transformation badge · badge explanation), the planner touchpoint, and the AERA block. Mark placeholder steps clearly.

2. **`## 2. Agent recharacterization table`** — one row per distinct v12 named agent (dedupe agents reused across steps; note every step each appears in). Columns: `v12 Agent Name` | `What it actually does` | `AI Refinery primary agent_class` | `Decomposes into (sub-agents)?` | `node_type` | `capabilities[]` | `Rationale`.

3. **`## 3. Orchestration architecture`** — describe the full AI Refinery composition in prose + a fenced ASCII/indented tree: the top-level orchestrator, each per-step `FlowSuperAgent`/`SuperAgent` with its ordered `nodes` and `edges`, the utility/tool/custom/research worker agents, the `HumanAgent` gates (the 3 governance touchpoints), and where the AERA service node is called. Imitate the demo-blueprint.md `DemandSurgePipeline` pattern.

4. **`## 4. New taxonomy proposals`** — any new `NODE_TYPES` or `CAPABILITIES` entries needed to faithfully represent AI Refinery classes/orchestration in the timeline (each: proposed id, label, suggested color, why). If none are needed, say so explicitly.

5. **`## 5. Build-ready JSON`** — a single fenced ```json block containing a `phases` array (the 8 steps, with `group` + FROM→TO + transformation metadata) and an `agents` array shaped to drop into the framework's `JOURNEY`, where each agent object carries the existing fields (`id`, `phaseId`, `title`, `type`, `status`, `description`, `capabilities`, `metric`, `optional`) PLUS new fields: `refineryClass` (the AI Refinery `agent_class`), `orchestration` (e.g. `{ role: "flow-super-agent", nodes: [...], edges: [...] }` or `{ role: "worker", parent: "<super-agent name>" }`), `tooling` (the 🔧 tags), `transformation` (`{ badge: "TRANSFORMED·AUGMENTED" | "ENABLED·AUTOMATED" | ..., note: "…" }`), and `aera` (the step's AERA note or `null`). Add a `touchpoints` array for the `HumanAgent` gates. This JSON is the literal handoff to prompt 003 — make it valid, complete, and self-consistent with sections 1–4.
</output_format>

<verification>
Before declaring complete, verify (state pass/fail for each in your summary):
1. All 8 v12 steps are represented (placeholders flagged, not fabricated).
2. Every distinct named v12 agent appears exactly once in the recharacterization table and is mapped to a real AI Refinery `agent_class` (no invented class names).
3. Every multi-step agent that warrants it has an explicit `FlowSuperAgent`/`SuperAgent` decomposition with concrete `nodes` + `edges`.
4. All 3 planner touchpoints are represented as `HumanAgent` gates in the architecture.
5. AERA is mapped as a called service node, not an orchestrator.
6. The section-5 JSON parses as valid JSON and references only `phaseId`s defined in its own `phases` array.
7. No client-confidential numbers were invented; v12 metrics preserved verbatim, gaps marked TBD.
</verification>

<success_criteria>
- A single `./research/npi-refinery-crosswalk.md` that a downstream Claude can read once and build the v2 timeline from, with zero re-derivation of the v12→Refinery mapping.
- Every bespoke v12 agent is re-expressed in standard AI Refinery vocabulary, with orchestration and utility-agent architecture made explicit where multi-agent.
- The build-ready JSON is valid and shape-compatible with `npi-timeline-framework.html`'s `JOURNEY` model.
</success_criteria>
