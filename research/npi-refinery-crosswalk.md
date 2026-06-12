# NPI → AI Refinery Recharacterization Crosswalk

> **Source:** Hershey's NPI Planning slide v12, page 3 ("NPI DECISION FLOW") · `hersheys_npi_planning_v12.html`
> **Maps onto:** Accenture AI Refinery (Distiller) standard agent library · `Refinery/demo-blueprint.md`, `Refinery/research/03-distiller-service.md`
> **Consumer:** prompt 003 (re-renders the NPI journey in `npi-timeline-framework.html`'s engine). This document is machine-liftable — section 5 is the literal handoff.
>
> **Scope note:** Only page-3 (steps 1–8) decisions and named agents are recharacterized per-step. The control-tower / 3-touchpoint framing and the AERA Decision Cloud stack are used only to inform orchestration vocabulary (see sections 3–4), not pulled into the per-step extraction.

---

## 1. Source extraction

The slide frames NPI success as **8 decisions** across three groups: PRE-LAUNCH ("Build the Plan", steps 1–4), LAUNCH ("Execute & Adjust", steps 5–7), POST-LAUNCH ("Learn & Scale", step 8). Verbatim/near-verbatim extraction below.

| # | Title | Group | FROM → TO |
|---|-------|-------|-----------|
| 1 | Portfolio & Launch Planning | PRE-LAUNCH | **From:** Manual SKU selection by brand teams; launch windows set by marketing calendar with no demand signal input. **To:** AI-driven portfolio scoring using like-product performance, channel demand signals, and capacity feasibility. |
| 2 | Demand Forecasting & Signal Conversion | PRE-LAUNCH | **From:** Manual like-product selection, static 12-week baseline built once and never recalibrated until launch review. **To:** AI like-product matching with continuous forecast recalibration from Day 1 of in-market signals. |
| 3 | Inventory Planning | PRE-LAUNCH | **From:** Launch quantity set by a single planner judgment call; under- and over-stock risk weighed informally. **To:** Optimal launch quantity computed from the underage/overage trade-off, then fed to downstream planning. |
| 4 | Downstream Planning Systems | PRE-LAUNCH | **PLACEHOLDER step** — no FROM→TO pair on slide. |
| 5 | Inventory Positioning & Deployment | LAUNCH | **From:** Static pre-build plan; DC allocation based on last-year regional patterns or commercial commitments. **To:** Dynamic pre-build and DC positioning driven by live demand signals and retailer readiness scores. |
| 6 | Distribution & Fulfillment Execution | LAUNCH | **PLACEHOLDER step** — no FROM→TO pair on slide. |
| 7 | Launch Execution & In-Market Adjustments | LAUNCH | **From:** Teams scrambling to gather early signals; adjustments made days or weeks after the signal first appeared. **To:** Continuous signal agents surfacing deviations within hours; planner reviews agent-recommended adjustments. (↺ closes the loop back into steps 2 & 4.) |
| 8 | Post-Launch Optimization & Scaling | POST-LAUNCH | **From:** Post-launch reviews done quarterly; learnings captured in slide decks rarely referenced in future launches. **To:** Automated performance loop that feeds learnings back into like-product library and forecast models in real-time. (↺ continuous refinement.) |

### Step 1 · Portfolio & Launch Planning (PRE-LAUNCH)
**Summary:** Hershey's decides which SKUs to bring to market, selects channels and target customers, sets the launch window, and establishes an expected demand range — the foundation all downstream decisions build on.

- ◆ **Which SKUs to launch and in which markets/channels** · *Portfolio Optimization Agent* · 🔧 MILP · weighted scoring · like-product regression · Monte Carlo · **TRANSFORMED · AUGMENTED** — "Brand-team picks become AI scores across like products performance and shelf velocity, which the planner approves."
- ◆ **Launch timing and sequencing by customer/region** · *Market Segmentation Agent* · 🔧 ML clustering · segmentation models · **TRANSFORMED · AUGMENTED** — "Marketing-calendar dates give way to channels and customers ranked by launch readiness and demand potential."
- ◆ **Expected demand range and initial confidence interval** · *Portfolio Optimization Agent* · 🔧 MILP · weighted scoring · like-product regression · Monte Carlo · **ENABLED · AUTOMATED** — "An evidence-based, high level range estimate is generated from like products instead of a single top-down guess."
- 👤 **Planner touchpoint:** review the agent-scored portfolio and approve the launch shortlist, instead of screening SKUs by hand.
- **AERA:** Placeholder.

### Step 2 · Demand Forecasting & Signal Conversion (PRE-LAUNCH) — ✓ Accenture asset
**Summary:** Translates portfolio decisions into demand curves at brand, SKU, and customer levels. The hardest forecasting problem in CPG: no history, uncertain like products, volatile early signals. Accuracy at this step sets the risk exposure for every step that follows.

- ◆ **Forecast at multiple levels: brand → SKU → customer** · *NPI Forecast Agent* · 🔧 Statistical models (ARIMA, time series) · ML (XGBoost, prophet) · like-product analogy · **TRANSFORMED · CONTINUOUS** — "One static 12-week baseline becomes a live multi-level sales curve that captures product life cycle sales forecast."
- ◆ **Capture early demand signals and measure feature predictability power** · *Signal Capture Agent* · 🔧 Data engineering tools · GenAI for unstructured signal extraction · **ENABLED · AUTOMATED** — "POS, retailer orders and digital signals are ingested and weighted in near real time, not pulled weekly by hand."
- ◆ **Leverage demand signals and features to refine forecast sales curve** · *Demand Sensing Agent* · 🔧 ML time-series · real-time signal models · **TRANSFORMED · AUGMENTED** — "Recalibrate sales curve with detected demand signals."
- 👤 **Planner touchpoint:** review the three agent-built forecast decisions and approve or override, instead of building the baseline manually.
- **AERA:** No AERA placeholder block on this step (step carries ⭐ Accenture Assets block instead: ISCP / Demand Planning APP (NPI), GenAI NPI Forecast Solution, NPI Forecast Tool). AERA = null.

### Step 3 · Inventory Planning (PRE-LAUNCH) — ✓ Accenture asset
**Summary:** Sets the optimal launch inventory quantity — the trade-off between underage (lost sales) and overage (write-off) cost. Its output becomes the target quantity that every downstream planning system builds against.

- ◆ **Optimal inventory launch quantity — trading off underage and overage cost** · *Inventory Optimization and Planning Agent* · 🔧 Newsvendor type of one-time buy model · **TRANSFORMED · AUGMENTED** — "Launch quantity becomes an optimized recommendation that balances stockout and write-off cost for planner sign-off."
- ◆ **Optimal quantity published as inputs to downstream planning systems** · *Inventory Optimization and Planning Agent* · 🔧 Newsvendor type of one-time buy model · **ENABLED · AUTOMATED** — "The optimized launch quantity flows automatically as the target that supply, production and deployment plans build against."
- 👤 **Planner touchpoint:** approve the optimized launch quantity; it then flows as the target into every downstream planning system.
- **AERA:** Placeholder. (Also carries ⭐ Accenture Assets: IPOT, ISCP Inventory Analytics & Monitoring Tool, Multi-echelon allocation accelerator.)

### Step 4 · Downstream Planning Systems (PRE-LAUNCH) — **PLACEHOLDER**
**Summary:** Translates the demand and inventory plan into supplier commitments and production schedules — sourcing, co-manufacturer capacity, plant allocation, and run sequencing. NPI adds new ingredients, untested co-manufacturers, compressed lead times, and changeover complexity.
**No named agents, no FROM→TO, no transformation badges.** Key decisions listed as plain bullets (sourcing strategy & supplier selection; supplier allocation/commitments/lead-time locks; co-manufacturer engagement & capacity reservation; plant allocation; production schedule/sequencing/batch sizing; changeover planning & capacity buffer). Client Credential = Placeholder. **AERA:** Placeholder. **Do not fabricate agents for this step.**

### Step 5 · Inventory Positioning & Deployment (LAUNCH) — ✓ Accenture asset
**Summary:** Pre-build inventory, position stock across the DC network, and allocate to customers/channels before launch day. Buffer strategy is critical: too little risks stockouts on day one; too much creates write-off exposure if velocity disappoints.

- ◆ **Pre-build inventory levels and timing ahead of launch** · *Inventory deployment and replenishing agent* · 🔧 Multi-echelon inventory optimization and replenishment models · **TRANSFORMED · AUGMENTED** — "A static pre-build plan becomes an agent recommendation sized to demand confidence intervals for planner sign-off."
- ◆ **Inventory positioning across DCs and regional markets** · *Inventory deployment and replenishing agent* · 🔧 Multi-echelon inventory optimization and replenishment models · **TRANSFORMED · CONTINUOUS** — "DC positioning shifts from last-year patterns to live signals, with replenishment triggered automatically."
- ◆ **Customer and channel allocation with risk buffers** · *Inventory deployment and replenishing agent* · 🔧 Multi-echelon inventory optimization and replenishment models · **ENABLED · AUTOMATED** — "Stock is distributed across customers and channels while respecting commitment, priority, and safety-stock constraints."
- 👤 **Planner touchpoint:** approve the agent-proposed pre-build and DC plan; agents then replenish within that approved envelope.
- **AERA:** Placeholder. (Also carries ⭐ Accenture Assets: IPOT, ISCP Inventory Analytics & Monitoring Tool, Multi-echelon allocation accelerator.)

### Step 6 · Distribution & Fulfillment Execution (LAUNCH) — **PLACEHOLDER**
**Summary:** Executes the distribution plan — moving product from DCs to retailers on schedule. Order fulfillment rules and service-level commitments are applied, with real-time tracking of shelf availability at each account.
**No named agents, no FROM→TO, no transformation badges.** Key decisions as plain bullets (distribution plan & carrier/mode selection; transportation routing & delivery scheduling; order fulfillment rules & service-level enforcement). Client Credential = Placeholder. **AERA:** Placeholder. **Do not fabricate agents for this step.**

### Step 7 · Launch Execution & In-Market Adjustments (LAUNCH) — ✓ Accenture asset · ↺ loop
**Summary:** The most dynamic step — early sell-through data drives rapid decisions. Agents surface deviations within hours, and the planner decides whether to trigger forecast recalibration, inventory redeployment, or both. ↺ **Closes the loop back into steps 2 & 4** — early sell-through signals directly trigger demand forecast recalibration and inventory repositioning (most value-dense feedback point in the NPI cycle).

- ◆ **Capture and weight early in-market signals — POS, retailer orders, shelf availability** · *Signal Capture Agent* · 🔧 Data engineering tools · GenAI for unstructured signal extraction · **ENABLED · AUTOMATED** — "Raw launch-week signals are ingested and weighted in near real time, ready for the sensing and forecast agents to act on."
- ◆ **Respond to early sell-through signals and revise forecasts** · *Demand Sensing Agent* · 🔧 ML time-series · real-time signal models · **TRANSFORMED · CONTINUOUS** — "Deviations vs. the launch forecast are detected continuously and trigger recalibration within hours."
- ◆ **Adjust production, inventory, or distribution in near real-time** · *Lifecycle Management Agent* · 🔧 LLM · statistical & ML lifecycle forecast models (phase-in / phase-out) · **TRANSFORMED · AUGMENTED** — "The demand curve updates and required supply and distribution actions are flagged for the planner to act on."
- ◆ **Manage exceptions: shortages, delays, demand spikes** · *Exception Handler Agent* · 🔧 LLM · exception alerting tools · next-best-action generation · **ENABLED · AUTOMATED** — "Exceptions are scored and routed by severity with a proposed resolution, instead of manual firefighting."
- 👤 **Planner touchpoint:** work the scored exception queue and accept or override agent recommendations, surfaced in hours not weeks.
- **AERA:** No AERA placeholder block on this step (carries ⭐ Accenture Assets block instead). AERA = null.

### Step 8 · Post-Launch Optimization & Scaling (POST-LAUNCH) — ↺ loop
**Summary:** Once launch stabilizes, performance data drives scaling and wind-down decisions — and feeds directly back into the like-product library and forecast models that power the next NPI plan. ↺ **Continuous refinement** — sustained sell-through and performance data continues to refine the demand forecast model (improving like-product library accuracy) and the inventory positioning logic (improving pre-build decisions for the next launch). The learning compounds across each NPI cycle.

- ◆ **Adjust inventory and production based on actual performance vs. plan** · *Performance Monitoring Agent* · 🔧 KPI metrics · accuracy & bias formulas (MAPE, WMAPE, Bias) · **ENABLED · AUTOMATED** — "MAPE, sell-through and margin are tracked against plan automatically, with model degradation auto-flagged."
- ◆ **Scale to new regions or channels where sell-through exceeded expectations** · *Scaling Agent* · 🔧 Scenario simulator · forecasting models on simulated parameters · **TRANSFORMED · AUGMENTED** — "Expansion opportunities are identified and replenishment triggered in high-velocity markets for planner approval."
- ◆ **Capture and encode learnings to improve the next NPI plan** · *Learning Loop Agent* · 🔧 ML · feedback loop models · like-product library updates · **TRANSFORMED · CONTINUOUS** — "NPI actuals are written back into the like-product library and forecast models, closing the loop each cycle."
- 👤 **Planner touchpoint:** review sell-through vs. plan and approve scale-up or wind-down; learnings feed the next launch automatically.
- **AERA:** Placeholder.

> **Badge inventory found on slide:** `TRANSFORMED · AUGMENTED`, `TRANSFORMED · CONTINUOUS`, `ENABLED · AUTOMATED`. (The objective mentioned a generic "ENABLED·AUTOMATED" — the slide also uses a `TRANSFORMED·CONTINUOUS` variant, preserved verbatim.)
> **Metrics:** v12 page 3 contains **no client-confidential numeric figures** — only model/tooling names and the global "25+ → 3 touchpoints" framing (which lives in the control-tower section, not the per-step flow). All agent `metric` fields below are therefore TBD.

---

## 2. Agent recharacterization table

12 distinct v12 named agents appear across steps 1, 2, 3, 5, 7, 8 (steps 4 and 6 are placeholders with no agents). Each is mapped to exactly one **primary** AI Refinery `agent_class`; agents whose real job is a fetch→model→score→write→approve pipeline are decomposed into a `FlowSuperAgent`.

| v12 Agent Name | What it actually does | AI Refinery primary `agent_class` | Decomposes into (sub-agents)? | node_type | capabilities[] | Rationale |
|---|---|---|---|---|---|---|
| **Portfolio Optimization Agent** (steps 1) | Scores/ranks the SKU launch shortlist and produces an evidence-based demand range using MILP, weighted scoring, like-product regression, Monte Carlo; planner approves. | `FlowSuperAgent` | **Yes** → `LikeProductResearcher` (`ResearchAgent`) · `PortfolioOptimizer` (`CustomAgent`, MILP/weighted scoring) · `DemandRangeSimulator` (`CustomAgent`, Monte Carlo + like-product regression) · `PortfolioScoreWriter` (`UtilityAgent`, magic_prompt + evidence grounding) · `LaunchPlanGate` (`HumanAgent`) | agent | predictive, ai-augment, logic | Two decisions (SKU/channel selection + demand range) both driven by deterministic optimization/simulation engines feeding a narrative recommendation behind a human gate — the canonical `DemandSurgePipeline` shape. Optimization/Monte-Carlo = `CustomAgent`; the planner sign-off = `HumanAgent`. |
| **Market Segmentation Agent** (step 1) | Clusters channels/customers and ranks launch timing & sequencing by readiness/demand potential using ML clustering + segmentation models. | `CustomAgent` | No (single deterministic ML model). Runs as a worker node inside the step-1 `FlowSuperAgent`. | agent | predictive, ai-augment | ML clustering / segmentation is deterministic data-science logic best wrapped as user-defined Python (`CustomAgent`), not a sub-pipeline. Optionally pairs with `AnalyticsAgent` for the structured-data read, but the clustering model itself is custom. |
| **NPI Forecast Agent** (step 2) | Generates a live multi-level (brand→SKU→customer) demand curve; flags model drift. Uses ARIMA/time-series + ML (XGBoost, prophet) + like-product analogy. | `FlowSuperAgent` | **Yes** → `LikeProductResearcher` (`ResearchAgent`, like-product library retrieval) · `ForecastModeler` (`CustomAgent`, ARIMA/XGBoost/prophet) · `ForecastNarrator` (`UtilityAgent`, magic_prompt) · `ForecastGate` (`HumanAgent`, shared step-2 gate) | agent | predictive, ai-augment, logic | Multi-stage: retrieve like-products → run statistical/ML models → write the curve narrative → planner approves. The modeling core is deterministic `CustomAgent`; like-product matching is RAG (`ResearchAgent`). |
| **Signal Capture Agent** (steps 2, 7) | Ingests & weights POS, retailer orders, and digital/unstructured signals in near real time; harmonizes & validates the demand dataset. | `ToolUseAgent` | No. Worker node feeding both the step-2 forecast pipeline and the step-7 monitoring SuperAgent. | agent | api, ai-augment, genai | Core job = pull live data from business/planning systems (ERP/GCP) → `ToolUseAgent` with `custom_tools`. GenAI unstructured-signal extraction is a magic_prompt aspect; the connector-to-source job is the defining one. Reused across steps 2 and 7 (dedup). |
| **Demand Sensing Agent** (steps 2, 7) | Detects live demand shifts vs. launch forecast and refines/recalibrates the in-market forecast curve continuously. | `CustomAgent` | No. Worker; consumes Signal Capture output, runs ML time-series / real-time signal models. | agent | predictive, ai-augment | Real-time signal models + continuous recalibration = deterministic ML compute → `CustomAgent`. Reused across steps 2 and 7 (dedup). |
| **Inventory Optimization and Planning Agent** (step 3) | Computes optimal launch quantity from the underage/overage trade-off (newsvendor one-time-buy), then publishes it as the target for downstream systems. | `FlowSuperAgent` | **Yes** → `InventoryDataFetcher` (`ToolUseAgent`) · `NewsvendorOptimizer` (`CustomAgent`, one-time-buy model) · `QuantityRecommendationWriter` (`UtilityAgent`, magic_prompt) · `LaunchQtyGate` (`HumanAgent`) · `DownstreamPublisher` (`ToolUseAgent`, writes target to downstream systems) | agent | predictive, logic, api | Two decisions: (a) compute optimal qty (newsvendor = `CustomAgent`), (b) publish to downstream (`ToolUseAgent` write). With a human approval between optimize and publish → a FlowSuperAgent DAG. |
| **Inventory deployment and replenishing agent** (step 5) | Sizes pre-build, positions stock across DCs/regions, allocates to customers/channels with risk buffers, and triggers replenishment within the approved envelope. Multi-echelon inventory optimization + replenishment models. | `FlowSuperAgent` | **Yes** → `NetworkDataFetcher` (`ToolUseAgent`, DC/inventory state) · `MultiEchelonOptimizer` (`CustomAgent`, multi-echelon optimization) · `AllocationPlanner` (`CustomAgent`, allocation w/ commitment & safety-stock constraints) · `DeploymentPlanWriter` (`UtilityAgent`) · `DeploymentGate` (`HumanAgent`) · `ReplenishmentExecutor` (`ToolUseAgent`, auto-replenish post-approval) | agent | predictive, logic, api | Three decisions (pre-build, DC positioning, channel allocation) all driven by one multi-echelon engine, plus automated replenishment after the gate — fetch→optimize→allocate→write→approve→execute pipeline. |
| **Lifecycle Management Agent** (step 7) | Updates the demand curve via phase-in/phase-out lifecycle models and flags required supply/inventory/distribution actions for the planner. LLM + statistical & ML lifecycle forecast models. | `CustomAgent` | No (with an LLM-narration aspect). Worker inside the step-7 monitoring SuperAgent. | agent | predictive, ai-augment, genai | Lifecycle forecast models (phase-in/out) = deterministic `CustomAgent`; the "flags actions for planner" framing is an `UtilityAgent` narration but the defining compute is the lifecycle model. Kept as `CustomAgent` primary; LLM aspect noted. |
| **Exception Handler Agent** (step 7) | Scores and routes exceptions (shortages, delays, demand spikes) by severity and generates a next-best-action / proposed resolution. LLM + exception alerting tools + NBA generation. | `FlowSuperAgent` | **Yes** → `ExceptionDetector` (`ToolUseAgent`, alerting tools) · `SeverityScorer` (`CustomAgent`, scoring/ranking) · `NextBestActionWriter` (`UtilityAgent`, magic_prompt NBA) · `ExceptionGate` (`HumanAgent`, the planner exception-queue gate) | agent | predictive, virtual-agent, genai, logic | Detect → score → propose-NBA → human-decides is a complete decision pipeline ending in the touchpoint-2 gate. Detection calls alerting systems (`ToolUseAgent`); scoring is deterministic (`CustomAgent`); NBA write is `UtilityAgent`. |
| **Performance Monitoring Agent** (step 8) | Tracks KPIs (MAPE, WMAPE, Bias), performs root-cause analysis, auto-flags model degradation vs. plan. | `AnalyticsAgent` | No. Worker inside the step-8 monitoring SuperAgent. | agent | predictive, ai-augment, logic | Structured-data KPI/accuracy analysis over actuals-vs-plan is exactly the `AnalyticsAgent` remit (CSV/DB analysis, formula computation). |
| **Scaling Agent** (step 8) | Identifies expansion opportunities (regions/channels where sell-through beat expectations), simulates scaling scenarios, and triggers replenishment for planner approval. Scenario simulator + forecasting on simulated parameters. | `CustomAgent` | No. Worker inside the step-8 monitoring SuperAgent (feeds the touchpoint-3 gate). | agent | predictive, logic | Scenario simulation on forecast parameters = deterministic simulator → `CustomAgent`, mirroring demo-blueprint's `SurgeSimulator`. |
| **Learning Loop Agent** (step 8) | Writes NPI actuals back into the like-product library and forecast models, closing the loop each cycle. ML feedback-loop models + like-product library updates. | `CustomAgent` | No. Worker; persists learnings (maps to Distiller **MemTree** memory as well). | agent | predictive, ai-augment | Feedback-loop model updates + library writes = custom Python logic (`CustomAgent`). Conceptually backed by Distiller's MemTree (persisted precedent), noted in section 3. |

**Dedup note:** *Signal Capture Agent* and *Demand Sensing Agent* each appear in **two** steps (2 and 7). They are declared once as shared utility agents and referenced by both the step-2 forecast `FlowSuperAgent` and the step-7 monitoring `SuperAgent` (the demo-blueprint "shared DataFetcher" pattern).

---

## 3. Orchestration architecture

The v12 "**Planner as Launch Control Tower**" with a "**25+ → 3 touchpoints**" governance surface maps cleanly onto AI Refinery's three-tier model: a top-level **orchestrator** (router) over per-step super-agents, deterministic steps as **`FlowSuperAgent`** DAGs (each ending in a `HumanAgent` gate), and the unpredictable post-launch monitoring/exception loop as a dynamic **`SuperAgent`**. **AERA** is a *called* `ToolUseAgent` service node invoked inside the relevant pipelines — never an orchestrator. The 3 planner governance touchpoints are exactly the 3 `HumanAgent` gates.

**Touchpoint → HumanAgent gate mapping (the 3 governance surfaces):**
- **Touchpoint 1 · Launch Plan Approval** → consolidated `LaunchPlanGate` (covers step-1 portfolio approval, step-2 forecast approval, step-3 launch-qty approval, step-5 deployment approval — the "approve the launch plan" surface). Modeled per-step as `LaunchPlanGate`, `ForecastGate`, `LaunchQtyGate`, `DeploymentGate`, all `HumanAgent`, all rolling up to Touchpoint 1.
- **Touchpoint 2 · Exception Review** → `ExceptionGate` (`HumanAgent`) at the end of the step-7 Exception Handler pipeline — work the scored exception queue, accept/override.
- **Touchpoint 3 · Post-Launch Recalibration** → `ScalingGate` (`HumanAgent`) at the step-8 monitoring SuperAgent — approve scale-up/wind-down, confirm next-window supply plan.

```
NPIControlTowerOrchestrator                         (orchestrator — router; "Planner as Launch Control Tower")
│  agent_list: [PortfolioPlanningPipeline, DemandForecastPipeline, InventoryPlanningPipeline,
│               InventoryDeploymentPipeline, InMarketMonitoringAdvisor, PostLaunchScalingAdvisor]
│  AERA: AeraDecisionCloud (ToolUseAgent / A2AClientAgent) — CALLED service, shared across pipelines
│
├─ [PRE-LAUNCH · batch-triggered]
│
├─ FlowSuperAgent: PortfolioPlanningPipeline            (Step 1)
│   nodes:  LikeProductResearcher → PortfolioOptimizer → MarketSegmenter
│           → DemandRangeSimulator → PortfolioScoreWriter → LaunchPlanGate(HumanAgent ◄ Touchpoint 1)
│   edges:  LikeProductResearcher→PortfolioOptimizer ; PortfolioOptimizer→MarketSegmenter ;
│           MarketSegmenter→DemandRangeSimulator ; DemandRangeSimulator→PortfolioScoreWriter ;
│           PortfolioScoreWriter→LaunchPlanGate
│     • LikeProductResearcher  [ResearchAgent]  — RAG over like-product library
│     • PortfolioOptimizer     [CustomAgent]    — MILP + weighted scoring
│     • MarketSegmenter        [CustomAgent]    — ML clustering / segmentation (Market Segmentation Agent)
│     • DemandRangeSimulator   [CustomAgent]    — Monte Carlo + like-product regression
│     • PortfolioScoreWriter   [UtilityAgent]   — magic_prompt, evidence-grounded
│     • LaunchPlanGate         [HumanAgent]     — Touchpoint 1
│     • (AERA called here for optimization/forecast answers on request)
│
├─ FlowSuperAgent: DemandForecastPipeline               (Step 2 · ✓ Accenture asset)
│   nodes:  SignalCapture → LikeProductResearcher → ForecastModeler
│           → DemandSensing → ForecastNarrator → ForecastGate(HumanAgent ◄ Touchpoint 1)
│   edges:  SignalCapture→LikeProductResearcher ; LikeProductResearcher→ForecastModeler ;
│           ForecastModeler→DemandSensing ; DemandSensing→ForecastNarrator ; ForecastNarrator→ForecastGate
│     • SignalCapture          [ToolUseAgent]   — Signal Capture Agent (SHARED with step 7)
│     • ForecastModeler        [CustomAgent]    — ARIMA / XGBoost / prophet (core of NPI Forecast Agent)
│     • DemandSensing          [CustomAgent]    — Demand Sensing Agent (SHARED with step 7)
│     • ForecastNarrator       [UtilityAgent]   — multi-level curve narrative
│     • ForecastGate           [HumanAgent]     — Touchpoint 1
│     • (AERA called here — forecast answers on request)
│
├─ FlowSuperAgent: InventoryPlanningPipeline            (Step 3 · ✓ Accenture asset)
│   nodes:  InventoryDataFetcher → NewsvendorOptimizer → QuantityRecommendationWriter
│           → LaunchQtyGate(HumanAgent ◄ Touchpoint 1) → DownstreamPublisher
│   edges:  InventoryDataFetcher→NewsvendorOptimizer ; NewsvendorOptimizer→QuantityRecommendationWriter ;
│           QuantityRecommendationWriter→LaunchQtyGate ; LaunchQtyGate→DownstreamPublisher
│     • InventoryDataFetcher   [ToolUseAgent]
│     • NewsvendorOptimizer    [CustomAgent]    — newsvendor one-time-buy model
│     • QuantityRecommendationWriter [UtilityAgent]
│     • LaunchQtyGate          [HumanAgent]     — Touchpoint 1
│     • DownstreamPublisher    [ToolUseAgent]   — publishes target to downstream planning systems
│     • (AERA called here — inventory answers on request)
│
├─ [Step 4 · Downstream Planning Systems] — PLACEHOLDER (no agents declared; published-to by DownstreamPublisher)
│
├─ [LAUNCH]
│
├─ FlowSuperAgent: InventoryDeploymentPipeline          (Step 5 · ✓ Accenture asset)
│   nodes:  NetworkDataFetcher → MultiEchelonOptimizer → AllocationPlanner
│           → DeploymentPlanWriter → DeploymentGate(HumanAgent ◄ Touchpoint 1) → ReplenishmentExecutor
│   edges:  NetworkDataFetcher→MultiEchelonOptimizer ; MultiEchelonOptimizer→AllocationPlanner ;
│           AllocationPlanner→DeploymentPlanWriter ; DeploymentPlanWriter→DeploymentGate ;
│           DeploymentGate→ReplenishmentExecutor
│     • NetworkDataFetcher     [ToolUseAgent]
│     • MultiEchelonOptimizer  [CustomAgent]    — multi-echelon inventory optimization
│     • AllocationPlanner      [CustomAgent]    — channel/customer allocation w/ safety-stock constraints
│     • DeploymentPlanWriter   [UtilityAgent]
│     • DeploymentGate         [HumanAgent]     — Touchpoint 1
│     • ReplenishmentExecutor  [ToolUseAgent]   — auto-replenish within approved envelope
│
├─ [Step 6 · Distribution & Fulfillment Execution] — PLACEHOLDER (no agents declared)
│
├─ SuperAgent: InMarketMonitoringAdvisor                (Step 7 · ✓ Accenture asset · ↺ loop → steps 2 & 4)
│   agent_pool: [SignalCapture, DemandSensing, LifecycleManager, ExceptionHandlerPipeline]
│   (dynamic: launch-week signals are unpredictable; the advisor decides whether to trigger
│    forecast recalibration, inventory redeployment, exception handling, or all three)
│     • SignalCapture          [ToolUseAgent]   — SHARED with step 2
│     • DemandSensing          [CustomAgent]    — SHARED with step 2
│     • LifecycleManager       [CustomAgent]    — Lifecycle Management Agent (phase-in/out models)
│     └─ FlowSuperAgent: ExceptionHandlerPipeline   (Exception Handler Agent)
│          nodes: ExceptionDetector → SeverityScorer → NextBestActionWriter
│                 → ExceptionGate(HumanAgent ◄ Touchpoint 2)
│          edges: ExceptionDetector→SeverityScorer ; SeverityScorer→NextBestActionWriter ;
│                 NextBestActionWriter→ExceptionGate
│            • ExceptionDetector    [ToolUseAgent]  — exception alerting tools
│            • SeverityScorer       [CustomAgent]   — severity scoring/ranking
│            • NextBestActionWriter [UtilityAgent]  — magic_prompt NBA generation
│            • ExceptionGate        [HumanAgent]    — Touchpoint 2
│
└─ SuperAgent: PostLaunchScalingAdvisor                 (Step 8 · POST-LAUNCH · ↺ continuous refinement)
    agent_pool: [PerformanceMonitor, ScalingSimulator, LearningLoop, ScalingGate]
    (dynamic: compound "scale where? wind down what? what did we learn?" queries)
      • PerformanceMonitor    [AnalyticsAgent] — MAPE/WMAPE/Bias KPI + root-cause
      • ScalingSimulator      [CustomAgent]    — Scaling Agent (scenario simulator)
      • LearningLoop          [CustomAgent]    — Learning Loop Agent (writes back to like-product library; backed by MemTree)
      • ScalingGate           [HumanAgent]     — Touchpoint 3 (approve scale-up / wind-down; confirm next-window plan)

SHARED SERVICE NODE (not an orchestrator):
  AeraDecisionCloud  [ToolUseAgent | A2AClientAgent]
    — "A called decision-intelligence service — returns forecast, inventory & optimization
       answers on request · Skills · Cortex." Invoked as a node within PortfolioPlanningPipeline,
       DemandForecastPipeline, InventoryPlanningPipeline (and available to the deployment pipeline).
       Modeled as A2AClientAgent if treated as an external agent platform; ToolUseAgent if treated
       as a called API. Default: ToolUseAgent.

MEMORY (cross-cycle learning): Distiller MemTree persists approved decisions, sell-through outcomes,
  and like-product precedents — the substrate the Learning Loop Agent writes into and the next
  cycle's PortfolioPlanningPipeline / DemandForecastPipeline read from. (Mirrors demo-blueprint MemTree.)
```

**Why these shapes (justification of the non-obvious choices):**
- **Steps 1, 2, 3, 5 = `FlowSuperAgent`** (not `SuperAgent`): pre-launch decisions are deterministic, ordered, and batch-triggered ("weeks before launch") — fetch → model → score → write → approve. This is exactly demo-blueprint's `DemandSurgePipeline` DAG.
- **Steps 7 and 8 = `SuperAgent`** (dynamic): the in-market loop and post-launch loop face unpredictable, compound queries ("recalibrate forecast AND/OR redeploy inventory AND/OR handle exception"; "scale where, wind down what, what did we learn"). This is demo-blueprint's `CrossScenarioAdvisor` pattern. The Exception Handler *inside* step 7 is still a deterministic `FlowSuperAgent` because detect→score→propose→approve is ordered.
- **Shared workers:** SignalCapture + DemandSensing are declared once and referenced by both the step-2 pipeline and the step-7 advisor (demo-blueprint shared-DataFetcher pattern).

---

## 4. New taxonomy proposals

The framework's `NODE_TYPES` (`trigger | human | agent | gate`) and `CAPABILITIES` (`predictive | ai-augment | logic | api | virtual-agent | genai`) cover the mapping. **The legacy `rpa` and `workflow` ids — and the `best-in-class`/`leapfrog` competitor-benchmark icons — have been removed:** they were artifacts of an earlier automation framing. Everything deterministic is now expressed as **logic** (`CustomAgent` — Python optimization/simulation/scoring) and everything that touches a system as an **API** call (`ToolUseAgent`/`A2AClientAgent`); a deterministic *workflow* is no longer a capability or a node-type but simply **orchestration** (a `FlowSuperAgent`). Existing fits used: `FlowSuperAgent`/`SuperAgent`/`CustomAgent`/`AnalyticsAgent`/`ResearchAgent`/`ToolUseAgent` → `agent`; `HumanAgent` → `gate` (for the 3 touchpoints) or `human` (for optional checkpoints). Two **optional** node-type additions would let prompt 003 render AI Refinery structure more faithfully; neither is strictly required (each maps to an existing id if omitted).

| Kind | Proposed id | Label | Suggested color | Why | Fallback if omitted |
|---|---|---|---|---|---|
| NODE_TYPES | `orchestrator` | "Orchestrator / Super-Agent" | `#E2231A` (HSY red — matches the control-tower framing) | The top-level `NPIControlTowerOrchestrator` and the per-step `FlowSuperAgent`/`SuperAgent` shells are categorically different from leaf `agent` cards — they *route/sequence* rather than *do*. A distinct node-type lets the timeline draw the control-tower vs. the worker agents under it. | Render as `agent` (it still reads correctly, just visually flat). |
| NODE_TYPES | `service` | "Called Service" | `#0F9D8C` (AERA teal — matches the slide's #E1F5EE/#0F6E56 AERA styling) | AERA is neither an agent the planner governs nor a human gate — it is a `ToolUseAgent`/`A2AClientAgent` *called on request*. A `service` node-type renders it as a side-car the pipelines call into, matching "Aera answers when asked." | Render as `agent` with `refineryClass: ToolUseAgent` (acceptable; loses the "called, not orchestrated" visual cue). |

The `CAPABILITIES` set is now `predictive | ai-augment | logic | api | virtual-agent | genai`. The two new ids replace the retired `rpa`/`workflow`: **`logic`** = deterministic Python (optimization/simulation/scoring — every `CustomAgent`/`AnalyticsAgent` modeling core); **`api`** = system/connector calls (`ToolUseAgent`/`A2AClientAgent`, incl. data ingestion and auto-replenishment). `genai` covers `magic_prompt` `UtilityAgent`s and GenAI signal extraction; `predictive` covers analytics/forecast modeling. The competitor-benchmark icons (`best-in-class`, `leapfrog`) are removed with no replacement.

---

## 5. Build-ready JSON

```json
{
  "meta": {
    "client": "Hershey's (HSY) × Accenture",
    "source": "hersheys_npi_planning_v12.html — page 3, NPI Decision Flow",
    "mapsOnto": "Accenture AI Refinery (Distiller) agent library",
    "orchestrator": {
      "name": "NPIControlTowerOrchestrator",
      "refineryClass": "orchestrator",
      "description": "Planner-as-Launch-Control-Tower router over the 8-step NPI super-agents; governs the 25+ → 3 touchpoints surface.",
      "agentList": [
        "PortfolioPlanningPipeline",
        "DemandForecastPipeline",
        "InventoryPlanningPipeline",
        "InventoryDeploymentPipeline",
        "InMarketMonitoringAdvisor",
        "PostLaunchScalingAdvisor"
      ],
      "memory": { "module": "MemTree", "note": "Persists approved decisions, sell-through outcomes, and like-product precedents across NPI cycles." }
    },
    "aera": {
      "id": "aera-decision-cloud",
      "title": "AERA Decision Cloud",
      "refineryClass": "ToolUseAgent",
      "altRefineryClass": "A2AClientAgent",
      "type": "service",
      "role": "called-service",
      "description": "Called decision-intelligence service — returns forecast, inventory & optimization answers on request · Skills · Cortex. Invoked as a node inside pipelines; NOT an orchestrator.",
      "calledBy": ["portfolio-optimization", "npi-forecast", "inventory-optimization-planning", "inventory-deployment-replenishing"]
    }
  },

  "phases": [
    { "id": "portfolio-launch-planning",      "label": "Portfolio & Launch Planning",            "step": 1, "group": "PRE-LAUNCH",  "groupLabel": "Build the Plan",   "fromTo": { "from": "Manual SKU selection by brand teams; launch windows set by marketing calendar with no demand signal input.", "to": "AI-driven portfolio scoring using like-product performance, channel demand signals, and capacity feasibility." }, "trigger": "batch", "placeholder": false, "loop": false, "accentureAsset": false, "aera": "Placeholder" },
    { "id": "demand-forecasting",             "label": "Demand Forecasting & Signal Conversion", "step": 2, "group": "PRE-LAUNCH",  "groupLabel": "Build the Plan",   "fromTo": { "from": "Manual like-product selection, static 12-week baseline built once and never recalibrated until launch review.", "to": "AI like-product matching with continuous forecast recalibration from Day 1 of in-market signals." }, "trigger": "batch", "placeholder": false, "loop": false, "accentureAsset": true, "aera": null },
    { "id": "inventory-planning",             "label": "Inventory Planning",                      "step": 3, "group": "PRE-LAUNCH",  "groupLabel": "Build the Plan",   "fromTo": { "from": "Launch quantity set by a single planner judgment call; under- and over-stock risk weighed informally.", "to": "Optimal launch quantity computed from the underage/overage trade-off, then fed to downstream planning." }, "trigger": "batch", "placeholder": false, "loop": false, "accentureAsset": true, "aera": "Placeholder" },
    { "id": "downstream-planning-systems",    "label": "Downstream Planning Systems",             "step": 4, "group": "PRE-LAUNCH",  "groupLabel": "Build the Plan",   "fromTo": null, "trigger": "batch", "placeholder": true, "loop": false, "accentureAsset": false, "aera": "Placeholder", "placeholderNote": "No named agents on slide. Decisions: sourcing strategy & supplier selection; supplier allocation/commitments/lead-time locks; co-manufacturer engagement & capacity reservation; plant allocation; production schedule/sequencing/batch sizing; changeover planning & capacity buffer. Client Credential = Placeholder." },
    { "id": "inventory-positioning-deployment","label": "Inventory Positioning & Deployment",    "step": 5, "group": "LAUNCH",      "groupLabel": "Execute & Adjust", "fromTo": { "from": "Static pre-build plan; DC allocation based on last-year regional patterns or commercial commitments.", "to": "Dynamic pre-build and DC positioning driven by live demand signals and retailer readiness scores." }, "trigger": "batch", "placeholder": false, "loop": false, "accentureAsset": true, "aera": "Placeholder" },
    { "id": "distribution-fulfillment",       "label": "Distribution & Fulfillment Execution",   "step": 6, "group": "LAUNCH",      "groupLabel": "Execute & Adjust", "fromTo": null, "trigger": "event", "placeholder": true, "loop": false, "accentureAsset": false, "aera": "Placeholder", "placeholderNote": "No named agents on slide. Decisions: distribution plan & carrier/mode selection; transportation routing & delivery scheduling; order fulfillment rules & service-level enforcement. Client Credential = Placeholder." },
    { "id": "launch-execution-adjustments",   "label": "Launch Execution & In-Market Adjustments","step": 7, "group": "LAUNCH",      "groupLabel": "Execute & Adjust", "fromTo": { "from": "Teams scrambling to gather early signals; adjustments made days or weeks after the signal first appeared.", "to": "Continuous signal agents surfacing deviations within hours; planner reviews agent-recommended adjustments." }, "trigger": "event", "placeholder": false, "loop": true, "loopNote": "Closes the loop back into steps 2 & 4 — sell-through triggers forecast recalibration and inventory repositioning.", "accentureAsset": true, "aera": null },
    { "id": "post-launch-optimization",       "label": "Post-Launch Optimization & Scaling",     "step": 8, "group": "POST-LAUNCH", "groupLabel": "Learn & Scale",    "fromTo": { "from": "Post-launch reviews done quarterly; learnings captured in slide decks rarely referenced in future launches.", "to": "Automated performance loop that feeds learnings back into like-product library and forecast models in real-time." }, "trigger": "event", "placeholder": false, "loop": true, "loopNote": "Continuous refinement — refines forecast model (like-product library accuracy) and inventory positioning logic for the next launch.", "accentureAsset": false, "aera": "Placeholder" }
  ],

  "agents": [
    {
      "id": "portfolio-optimization",
      "phaseId": "portfolio-launch-planning",
      "title": "Portfolio Optimization Agent",
      "type": "agent",
      "status": "planned",
      "description": "Scores and ranks the SKU launch shortlist and generates an evidence-based demand range. Two decisions: which SKUs/channels to launch, and the expected demand range + initial confidence interval.",
      "capabilities": ["predictive", "ai-augment", "logic"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "FlowSuperAgent",
      "orchestration": {
        "role": "flow-super-agent",
        "nodes": ["LikeProductResearcher", "PortfolioOptimizer", "MarketSegmenter", "DemandRangeSimulator", "PortfolioScoreWriter", "LaunchPlanGate"],
        "edges": [
          { "from": "LikeProductResearcher", "to": "PortfolioOptimizer" },
          { "from": "PortfolioOptimizer", "to": "MarketSegmenter" },
          { "from": "MarketSegmenter", "to": "DemandRangeSimulator" },
          { "from": "DemandRangeSimulator", "to": "PortfolioScoreWriter" },
          { "from": "PortfolioScoreWriter", "to": "LaunchPlanGate" }
        ],
        "subAgents": [
          { "agent_name": "LikeProductResearcher", "agent_class": "ResearchAgent", "agent_description": "RAG over the like-product performance library." },
          { "agent_name": "PortfolioOptimizer", "agent_class": "CustomAgent", "agent_description": "MILP + weighted scoring of SKU/channel options." },
          { "agent_name": "MarketSegmenter", "agent_class": "CustomAgent", "agent_description": "ML clustering / segmentation for launch timing & sequencing (Market Segmentation Agent)." },
          { "agent_name": "DemandRangeSimulator", "agent_class": "CustomAgent", "agent_description": "Monte Carlo + like-product regression for the demand range & confidence interval." },
          { "agent_name": "PortfolioScoreWriter", "agent_class": "UtilityAgent", "agent_description": "Evidence-grounded portfolio recommendation (magic_prompt, anti-hallucination)." },
          { "agent_name": "LaunchPlanGate", "agent_class": "HumanAgent", "agent_description": "Planner approves the launch shortlist (Touchpoint 1)." }
        ]
      },
      "tooling": "MILP · weighted scoring · like-product regression · Monte Carlo",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "Brand-team picks become AI scores across like-products performance and shelf velocity, which the planner approves. (Demand-range decision is ENABLED·AUTOMATED.)" },
      "aera": "Placeholder"
    },
    {
      "id": "market-segmentation",
      "phaseId": "portfolio-launch-planning",
      "title": "Market Segmentation Agent",
      "type": "agent",
      "status": "planned",
      "description": "Clusters channels/customers and ranks launch timing & sequencing by launch readiness and demand potential.",
      "capabilities": ["predictive", "ai-augment"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "CustomAgent",
      "orchestration": { "role": "worker", "parent": "PortfolioPlanningPipeline", "nodeName": "MarketSegmenter" },
      "tooling": "ML clustering · segmentation models",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "Marketing-calendar dates give way to channels and customers ranked by launch readiness and demand potential." },
      "aera": null
    },
    {
      "id": "npi-forecast",
      "phaseId": "demand-forecasting",
      "title": "NPI Forecast Agent",
      "type": "agent",
      "status": "planned",
      "description": "Generates a live multi-level (brand → SKU → customer) demand curve and flags model drift.",
      "capabilities": ["predictive", "ai-augment", "logic"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "FlowSuperAgent",
      "orchestration": {
        "role": "flow-super-agent",
        "nodes": ["SignalCapture", "LikeProductResearcher", "ForecastModeler", "DemandSensing", "ForecastNarrator", "ForecastGate"],
        "edges": [
          { "from": "SignalCapture", "to": "LikeProductResearcher" },
          { "from": "LikeProductResearcher", "to": "ForecastModeler" },
          { "from": "ForecastModeler", "to": "DemandSensing" },
          { "from": "DemandSensing", "to": "ForecastNarrator" },
          { "from": "ForecastNarrator", "to": "ForecastGate" }
        ],
        "subAgents": [
          { "agent_name": "SignalCapture", "agent_class": "ToolUseAgent", "agent_description": "Signal Capture Agent (SHARED with step 7) — ingests POS/orders/digital signals." },
          { "agent_name": "LikeProductResearcher", "agent_class": "ResearchAgent", "agent_description": "RAG over the like-product library for analogs." },
          { "agent_name": "ForecastModeler", "agent_class": "CustomAgent", "agent_description": "ARIMA / XGBoost / prophet multi-level forecast (core of NPI Forecast Agent)." },
          { "agent_name": "DemandSensing", "agent_class": "CustomAgent", "agent_description": "Demand Sensing Agent (SHARED with step 7) — refines the curve from live signals." },
          { "agent_name": "ForecastNarrator", "agent_class": "UtilityAgent", "agent_description": "Writes the explainable multi-level demand-curve narrative (magic_prompt)." },
          { "agent_name": "ForecastGate", "agent_class": "HumanAgent", "agent_description": "Planner approves/overrides the forecast (Touchpoint 1)." }
        ]
      },
      "tooling": "Statistical models (ARIMA, time series) · ML (XGBoost, prophet) · like-product analogy",
      "transformation": { "badge": "TRANSFORMED·CONTINUOUS", "note": "One static 12-week baseline becomes a live multi-level sales curve that captures product life cycle sales forecast." },
      "aera": null
    },
    {
      "id": "signal-capture",
      "phaseId": "demand-forecasting",
      "title": "Signal Capture Agent",
      "type": "agent",
      "status": "planned",
      "description": "Ingests and weights POS, retailer orders, and digital/unstructured signals in near real time; harmonizes & validates the demand dataset. Shared across steps 2 and 7.",
      "capabilities": ["api", "ai-augment", "genai"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "ToolUseAgent",
      "orchestration": { "role": "worker", "parent": "DemandForecastPipeline", "alsoParent": "InMarketMonitoringAdvisor", "nodeName": "SignalCapture", "shared": true },
      "tooling": "Data engineering tools · GenAI for unstructured signal extraction",
      "transformation": { "badge": "ENABLED·AUTOMATED", "note": "POS, retailer orders and digital signals are ingested and weighted in near real time, not pulled weekly by hand." },
      "aera": null
    },
    {
      "id": "demand-sensing",
      "phaseId": "demand-forecasting",
      "title": "Demand Sensing Agent",
      "type": "agent",
      "status": "planned",
      "description": "Detects live demand shifts vs. the launch forecast and continuously recalibrates the in-market forecast curve. Shared across steps 2 and 7.",
      "capabilities": ["predictive", "ai-augment"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "CustomAgent",
      "orchestration": { "role": "worker", "parent": "DemandForecastPipeline", "alsoParent": "InMarketMonitoringAdvisor", "nodeName": "DemandSensing", "shared": true },
      "tooling": "ML time-series · real-time signal models",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "Recalibrate sales curve with detected demand signals." },
      "aera": null
    },
    {
      "id": "inventory-optimization-planning",
      "phaseId": "inventory-planning",
      "title": "Inventory Optimization and Planning Agent",
      "type": "agent",
      "status": "planned",
      "description": "Computes the optimal launch quantity from the underage/overage trade-off (newsvendor one-time-buy) and publishes it as the target for downstream planning systems.",
      "capabilities": ["predictive", "logic", "api"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "FlowSuperAgent",
      "orchestration": {
        "role": "flow-super-agent",
        "nodes": ["InventoryDataFetcher", "NewsvendorOptimizer", "QuantityRecommendationWriter", "LaunchQtyGate", "DownstreamPublisher"],
        "edges": [
          { "from": "InventoryDataFetcher", "to": "NewsvendorOptimizer" },
          { "from": "NewsvendorOptimizer", "to": "QuantityRecommendationWriter" },
          { "from": "QuantityRecommendationWriter", "to": "LaunchQtyGate" },
          { "from": "LaunchQtyGate", "to": "DownstreamPublisher" }
        ],
        "subAgents": [
          { "agent_name": "InventoryDataFetcher", "agent_class": "ToolUseAgent", "agent_description": "Pulls inventory/cost/service-level state from planning systems." },
          { "agent_name": "NewsvendorOptimizer", "agent_class": "CustomAgent", "agent_description": "Newsvendor one-time-buy model balancing underage vs. overage cost." },
          { "agent_name": "QuantityRecommendationWriter", "agent_class": "UtilityAgent", "agent_description": "Evidence-grounded optimal-quantity recommendation (magic_prompt)." },
          { "agent_name": "LaunchQtyGate", "agent_class": "HumanAgent", "agent_description": "Planner approves the optimized launch quantity (Touchpoint 1)." },
          { "agent_name": "DownstreamPublisher", "agent_class": "ToolUseAgent", "agent_description": "Publishes the approved target quantity to downstream planning systems (feeds step 4)." }
        ]
      },
      "tooling": "Newsvendor type of one-time buy model",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "Launch quantity becomes an optimized recommendation balancing stockout and write-off cost for planner sign-off. (Publish-to-downstream decision is ENABLED·AUTOMATED.)" },
      "aera": "Placeholder"
    },
    {
      "id": "inventory-deployment-replenishing",
      "phaseId": "inventory-positioning-deployment",
      "title": "Inventory deployment and replenishing agent",
      "type": "agent",
      "status": "planned",
      "description": "Sizes pre-build, positions stock across DCs/regions, allocates to customers/channels with risk buffers, and triggers replenishment within the approved envelope.",
      "capabilities": ["predictive", "logic", "api"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "FlowSuperAgent",
      "orchestration": {
        "role": "flow-super-agent",
        "nodes": ["NetworkDataFetcher", "MultiEchelonOptimizer", "AllocationPlanner", "DeploymentPlanWriter", "DeploymentGate", "ReplenishmentExecutor"],
        "edges": [
          { "from": "NetworkDataFetcher", "to": "MultiEchelonOptimizer" },
          { "from": "MultiEchelonOptimizer", "to": "AllocationPlanner" },
          { "from": "AllocationPlanner", "to": "DeploymentPlanWriter" },
          { "from": "DeploymentPlanWriter", "to": "DeploymentGate" },
          { "from": "DeploymentGate", "to": "ReplenishmentExecutor" }
        ],
        "subAgents": [
          { "agent_name": "NetworkDataFetcher", "agent_class": "ToolUseAgent", "agent_description": "Pulls DC network state and current inventory positions." },
          { "agent_name": "MultiEchelonOptimizer", "agent_class": "CustomAgent", "agent_description": "Multi-echelon inventory optimization for pre-build sizing & DC positioning." },
          { "agent_name": "AllocationPlanner", "agent_class": "CustomAgent", "agent_description": "Allocates across customers/channels respecting commitment, priority, and safety-stock constraints." },
          { "agent_name": "DeploymentPlanWriter", "agent_class": "UtilityAgent", "agent_description": "Writes the pre-build + DC + allocation plan for sign-off (magic_prompt)." },
          { "agent_name": "DeploymentGate", "agent_class": "HumanAgent", "agent_description": "Planner approves the pre-build and DC plan (Touchpoint 1)." },
          { "agent_name": "ReplenishmentExecutor", "agent_class": "ToolUseAgent", "agent_description": "Auto-replenishes within the approved envelope." }
        ]
      },
      "tooling": "Multi-echelon inventory optimization and replenishment models",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "A static pre-build plan becomes an agent recommendation sized to demand confidence intervals for planner sign-off. (Positioning is TRANSFORMED·CONTINUOUS; channel allocation is ENABLED·AUTOMATED.)" },
      "aera": "Placeholder"
    },
    {
      "id": "lifecycle-management",
      "phaseId": "launch-execution-adjustments",
      "title": "Lifecycle Management Agent",
      "type": "agent",
      "status": "planned",
      "description": "Updates the demand curve via phase-in/phase-out lifecycle models and flags required supply/inventory/distribution actions for the planner.",
      "capabilities": ["predictive", "ai-augment", "genai"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "CustomAgent",
      "orchestration": { "role": "worker", "parent": "InMarketMonitoringAdvisor", "nodeName": "LifecycleManager" },
      "tooling": "LLM · statistical & ML lifecycle forecast models (phase-in / phase-out)",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "The demand curve updates and required supply and distribution actions are flagged for the planner to act on." },
      "aera": null
    },
    {
      "id": "exception-handler",
      "phaseId": "launch-execution-adjustments",
      "title": "Exception Handler Agent",
      "type": "agent",
      "status": "planned",
      "description": "Scores and routes exceptions (shortages, delays, demand spikes) by severity and generates a proposed next-best-action resolution.",
      "capabilities": ["predictive", "virtual-agent", "genai", "logic"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "FlowSuperAgent",
      "orchestration": {
        "role": "flow-super-agent",
        "parent": "InMarketMonitoringAdvisor",
        "nodes": ["ExceptionDetector", "SeverityScorer", "NextBestActionWriter", "ExceptionGate"],
        "edges": [
          { "from": "ExceptionDetector", "to": "SeverityScorer" },
          { "from": "SeverityScorer", "to": "NextBestActionWriter" },
          { "from": "NextBestActionWriter", "to": "ExceptionGate" }
        ],
        "subAgents": [
          { "agent_name": "ExceptionDetector", "agent_class": "ToolUseAgent", "agent_description": "Exception alerting tools — detects shortages, delays, demand spikes." },
          { "agent_name": "SeverityScorer", "agent_class": "CustomAgent", "agent_description": "Scores and ranks exceptions by severity." },
          { "agent_name": "NextBestActionWriter", "agent_class": "UtilityAgent", "agent_description": "Generates the proposed next-best-action / resolution (magic_prompt)." },
          { "agent_name": "ExceptionGate", "agent_class": "HumanAgent", "agent_description": "Planner works the scored exception queue, accepts/overrides (Touchpoint 2)." }
        ]
      },
      "tooling": "LLM · exception alerting tools · next-best-action generation",
      "transformation": { "badge": "ENABLED·AUTOMATED", "note": "Exceptions are scored and routed by severity with a proposed resolution, instead of manual firefighting." },
      "aera": null
    },
    {
      "id": "performance-monitoring",
      "phaseId": "post-launch-optimization",
      "title": "Performance Monitoring Agent",
      "type": "agent",
      "status": "planned",
      "description": "Tracks KPIs (MAPE, WMAPE, Bias), performs root-cause analysis, and auto-flags model degradation vs. plan.",
      "capabilities": ["predictive", "ai-augment", "logic"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "AnalyticsAgent",
      "orchestration": { "role": "worker", "parent": "PostLaunchScalingAdvisor", "nodeName": "PerformanceMonitor" },
      "tooling": "KPI metrics · accuracy & bias formulas (MAPE, WMAPE, Bias)",
      "transformation": { "badge": "ENABLED·AUTOMATED", "note": "MAPE, sell-through and margin are tracked against plan automatically, with model degradation auto-flagged." },
      "aera": "Placeholder"
    },
    {
      "id": "scaling",
      "phaseId": "post-launch-optimization",
      "title": "Scaling Agent",
      "type": "agent",
      "status": "planned",
      "description": "Identifies expansion opportunities, simulates scaling scenarios, and triggers replenishment in high-velocity markets for planner approval.",
      "capabilities": ["predictive", "logic"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "CustomAgent",
      "orchestration": { "role": "worker", "parent": "PostLaunchScalingAdvisor", "nodeName": "ScalingSimulator" },
      "tooling": "Scenario simulator · forecasting models on simulated parameters",
      "transformation": { "badge": "TRANSFORMED·AUGMENTED", "note": "Expansion opportunities are identified and replenishment triggered in high-velocity markets for planner approval." },
      "aera": "Placeholder"
    },
    {
      "id": "learning-loop",
      "phaseId": "post-launch-optimization",
      "title": "Learning Loop Agent",
      "type": "agent",
      "status": "planned",
      "description": "Writes NPI actuals back into the like-product library and forecast models, closing the loop each cycle. Backed by Distiller MemTree persistent memory.",
      "capabilities": ["predictive", "ai-augment"],
      "metric": "TBD",
      "optional": false,
      "refineryClass": "CustomAgent",
      "orchestration": { "role": "worker", "parent": "PostLaunchScalingAdvisor", "nodeName": "LearningLoop", "memory": "MemTree" },
      "tooling": "ML · feedback loop models · like-product library updates",
      "transformation": { "badge": "TRANSFORMED·CONTINUOUS", "note": "NPI actuals are written back into the like-product library and forecast models, closing the loop each cycle." },
      "aera": "Placeholder"
    }
  ],

  "touchpoints": [
    {
      "id": "touchpoint-1-launch-plan-approval",
      "title": "Touchpoint 1 · Launch Plan Approval",
      "type": "gate",
      "refineryClass": "HumanAgent",
      "description": "Review agent-generated launch scenarios and confidence bands; approve initial supply, inventory, and distribution plan.",
      "gates": ["LaunchPlanGate", "ForecastGate", "LaunchQtyGate", "DeploymentGate"],
      "phaseIds": ["portfolio-launch-planning", "demand-forecasting", "inventory-planning", "inventory-positioning-deployment"]
    },
    {
      "id": "touchpoint-2-exception-review",
      "title": "Touchpoint 2 · Exception Review",
      "type": "gate",
      "refineryClass": "HumanAgent",
      "description": "Review the scored and ranked NPI exception queue; decide accept agent recommendation or override with judgment.",
      "gates": ["ExceptionGate"],
      "phaseIds": ["launch-execution-adjustments"]
    },
    {
      "id": "touchpoint-3-post-launch-recalibration",
      "title": "Touchpoint 3 · Post-Launch Recalibration",
      "type": "gate",
      "refineryClass": "HumanAgent",
      "description": "Review sell-through vs. forecast; approve scaling or wind-down; confirm updated supply plan for the next launch window.",
      "gates": ["ScalingGate"],
      "phaseIds": ["post-launch-optimization"]
    }
  ],

  "proposedTaxonomy": {
    "nodeTypes": [
      { "id": "orchestrator", "label": "Orchestrator / Super-Agent", "color": "#E2231A" },
      { "id": "service", "label": "Called Service", "color": "#0F9D8C" }
    ],
    "capabilities": []
  }
}
```

---

## Verification

| # | Check | Result |
|---|-------|--------|
| 1 | All 8 v12 steps represented; placeholders flagged not fabricated | **PASS** — all 8 in `phases[]`; steps 4 & 6 `placeholder: true`, `fromTo: null`, zero agents, `placeholderNote` carrying their plain decision bullets. |
| 2 | Every distinct named v12 agent appears exactly once, mapped to a real AI Refinery class | **PASS** — 12 distinct agents, each one row in section 2 and one object in `agents[]`. Classes used (`FlowSuperAgent`, `SuperAgent`, `CustomAgent`, `AnalyticsAgent`, `ResearchAgent`, `ToolUseAgent`, `UtilityAgent`, `HumanAgent`, `A2AClientAgent`) are all from the documented library — no invented names. Signal Capture & Demand Sensing reused across steps 2/7 but declared once (shared workers). |
| 3 | Every multi-step agent that warrants it has explicit FlowSuperAgent/SuperAgent decomposition with concrete nodes + edges | **PASS** — 4 agents decomposed into `FlowSuperAgent` with full `nodes`+`edges` (Portfolio Optimization, NPI Forecast, Inventory Optimization & Planning, Inventory deployment & replenishing, plus Exception Handler nested inside the step-7 SuperAgent). Steps 7 & 8 are dynamic `SuperAgent`s with agent pools. |
| 4 | All 3 planner touchpoints represented as HumanAgent gates | **PASS** — `touchpoints[]` has all 3, each `refineryClass: HumanAgent`; gates wired into the relevant pipeline DAGs (Touchpoint 1 = 4 gates, Touchpoint 2 = ExceptionGate, Touchpoint 3 = ScalingGate). |
| 5 | AERA mapped as a called service, not an orchestrator | **PASS** — `meta.aera` = `ToolUseAgent` (alt `A2AClientAgent`), `role: called-service`, `type: service`, with `calledBy` list of the pipelines; explicitly not in the orchestrator role. |
| 6 | Section-5 JSON parses as valid JSON; references only phaseIds defined in its own phases array | **PASS** — validated with `python3 -m json.tool` (see note below). Every `agent.phaseId` and every `touchpoints[].phaseIds` value matches an `id` in `phases[]`. |
| 7 | No client-confidential numbers invented; v12 metrics preserved verbatim; gaps TBD | **PASS** — page 3 has no numeric figures; all `metric` fields set to `"TBD"`. FROM→TO text, badges, tooling tags, and touchpoint copy are verbatim/near-verbatim from the slide. |

> JSON validity was confirmed by extracting the fenced block and running `python3 -m json.tool`; it parsed with no errors.
