<objective>
**Think first, then build — in a new file.** Two stages, in order:

**Stage 1 — Reason about the visualization.** Produce a short design-reasoning document that works out *how* to show the technology / data-foundation that sits **beneath** the existing NPI timeline: SAP S4, Snowflake/Databricks, Kinaxis, and Aera Technologies — and how data captured from S4 and Kinaxis flows **up** into the Aera skills that support New Product Introduction. Propose 2–3 distinct visual approaches, weigh them, and recommend one. **Do not build yet.**

**Stage 2 — Build the recommendation into `npi-timeline-framework-v2A.html`** — a NEW file that copies the proven `npi-timeline-framework-v2.html` engine and adds a **persistent technology-foundation band pinned below the timeline**, with each Aera skill **badged** as *deterministic* vs *agentic*. **The master v2 file is never edited** — v2A is additive so the two can be diffed and discussed.

End goal: a consultant can open one offline HTML page, scroll the 8-step NPI journey, and — in a band underneath — see the data-and-systems substrate (S4 · Databricks · Kinaxis · Aera skills) feeding up into the agents above, with the deterministic-vs-agentic nature of each Aera skill made explicit. The "v2A" naming exists precisely so this is a *thinking artifact* to react to, not a replacement.
</objective>

<context>
This is the Accenture × Hershey's (HSY) NPI consulting workspace. **Read `./CLAUDE.md` first** for the hard constraints and the data/engine architecture — they are non-negotiable and summarized again under `<constraints>`.

**Read these, in order, before Stage 1:**

1. **The engine to extend (the base for v2A — DO NOT EDIT IT):**
@/Users/vincent.wicker/Documents/area_agent_NPI/npi-timeline-framework-v2.html
Study its data objects (`CAPABILITIES`, `NODE_TYPES`, `REFINERY_CLASSES`, `JOURNEY` with `phases`/`agents`/`touchpoints`, `GLYPHS`) and the render engine below them (phase bars on a continuous numbered arrow, group banding PRE-LAUNCH/LAUNCH/POST-LAUNCH, card rendering, click-to-expand drawer, capability/status/refineryClass filters, drag-to-pan/shift-wheel scroll). You are **copying this file's engine and CSS as v2A's foundation**, then adding a new bottom band. Preserve everything that works.

2. **The crosswalk (reconcile against it — do not duplicate):**
@/Users/vincent.wicker/Documents/area_agent_NPI/research/npi-refinery-crosswalk.md
**Critical:** v2 already contains agents derived from this crosswalk — including a `DemandSensing` worker and a `SignalCapture` worker. Before inventing new nodes for the foundation band, **map the three Aera skills below onto what already exists** and flag overlaps explicitly (Stage 1). Note the crosswalk's established stance: **Aera is a *called* decision-intelligence service** (a `ToolUseAgent`/`A2AClientAgent` node that returns forecast/inventory/optimization answers on request), **not an orchestrator**. The foundation band must reflect Aera as a called-service layer, consistent with this.

**The domain to represent (the user's own framing — treat as source of truth; do not invent client-confidential figures):**

The organization's technology landscape, conceptually layered bottom-to-top:
- **SAP S4** — system of record: finished-goods planning, raw-material planning, master data.
- **Snowflake / Databricks** — the data platform where captured transactional data lands (treat as one consolidated data layer).
- **Kinaxis** — demand planning (incl. new-product-launch inputs), supply planning, and inventory pre-builds. *(The user variously said "Canaxis/Conaxis" — this is **Kinaxis**.)*
- **Aera Technologies** — delivers **"skills."** Some skills are **deterministic**, some are **agentic**. This deterministic-vs-agentic distinction is the central thing to surface.

The Aera skills being built (each `skillType` noted; place each where it supports the NPI continuum):
- **Demand Sensing** — *deterministic*. Reads demand and builds the curves that show what is happening at the shelf; handles the complexity of measuring point-of-sale (POS) scans and mapping them up to cases; gives planners visibility. *(Reconcile with v2's existing `DemandSensing`/`SignalCapture` workers — likely the same capability, surfaced as an Aera skill.)*
- **On-Shelf Availability (OSA)** — *deterministic*. Reaches into distributor/customer shelf and warehouse data to see what is on shelf / in the warehouse; when service levels slip, pushes out corrective actions.
- **Manufacturing Frozen-Period Cut** — *deterministic*. During the production frozen period, cuts production when demand is not manifesting.
- These skills drive **supply planning and inventory pre-builds**, written back into **Kinaxis**.

Data-flow intent to make visible: **S4 (master data, FG/RM plans) + Kinaxis (demand/supply plans, launch inputs) → captured into Snowflake/Databricks → consumed by Aera skills (Demand Sensing, OSA, Frozen-Period Cut) → which support / are called by the NPI agents on the timeline above; results (pre-builds, production cuts) loop back into Kinaxis.**
</context>

<stage_1_reasoning>
**Think deeply before proposing anything — do not write code in this stage.** Consider multiple valid ways to render a foundation substrate under a horizontally-scrolling timeline, and justify your choice. Address, at minimum:

1. **Layered model.** Translate the user's narrative into a clean, explicit layer stack (e.g. *Systems of Record (S4)* → *Data Platform (Snowflake/Databricks)* → *Planning (Kinaxis)* → *Aera Skills*). State each layer's nodes and one-line role. Decide the canonical row order bottom-to-top and why.

2. **Reconciliation with v2.** Cross-reference the three Aera skills against agents already in v2/the crosswalk (esp. `DemandSensing`, `SignalCapture`). For each Aera skill, state: is it the same as an existing agent, a generalization of several, or genuinely new? Recommend whether the foundation band should *reference* existing timeline agents or introduce its own nodes — and how to avoid double-representation.

3. **Alignment to the timeline.** The chosen layout is a **persistent band pinned below the timeline**. Work out how foundation nodes relate to the 8 phases above: are Aera skills aligned under the specific NPI steps they support (Demand Sensing → demand/forecast & in-market steps; OSA → launch execution & post-launch; Frozen-Period Cut → supply/launch execution), or is the substrate a global layer with upward connectors only? Recommend, with reasoning, how to show "feeds up into" without visual clutter at 8-column width.

4. **Deterministic vs agentic encoding.** Per the decision already made, this is **badge + legend only — no new filter control.** Specify the badge treatment (label, placement, accent) for `skillType: "deterministic" | "agentic"` so it is unmistakable yet consistent with the existing Accenture purple aesthetic. Note that all three named skills are deterministic today; design the agentic badge anyway so future skills render correctly (data-driven).

5. **Aera as called service.** Reaffirm and show how the band represents Aera as a *called* service layer (consistent with the crosswalk), not an orchestrator — e.g. connectors read "called by" the agents above rather than "controls."

6. **2–3 candidate approaches, then a recommendation.** Sketch (in prose / ASCII) at least two distinct layouts for the band+connectors (e.g. *full-width layered rows with upward flow arrows* vs *per-phase substrate columns* vs *band + click-to-expand architecture diagram*). Give tradeoffs (legibility, clutter, fidelity to data flow, build complexity) and pick one, explaining why. The persistent-band-below-timeline direction is fixed; this step decides *how* within it.

7. **Open questions / assumptions.** List anything you had to assume (e.g. exact S4↔Kinaxis↔Databricks flow direction, whether OSA reads customer data directly or via Databricks) so the user can correct it before or after the build.

**Write Stage 1 output to:** `./research/npi-foundation-layer-design.md`
</stage_1_reasoning>

<stage_2_build>
Implement the **recommended** approach from Stage 1 by copying v2's engine into a new file and extending it. The foundation band must be as **data-driven** as the rest of the framework.

1. **New data object — the foundation.** Add one editable, clearly-commented data object near the top of the `<script>` (e.g. `FOUNDATION`) that drives the entire band. Shape it roughly as:
   - `layers`: ordered bottom-to-top, each `{ id, label, kind }` (e.g. `kind: "system-of-record" | "data-platform" | "planning" | "aera-skills"`).
   - `nodes`: each `{ id, layerId, label, system, note }` for the boxes in each layer (S4, Snowflake/Databricks, Kinaxis, and the Aera skills).
   - Aera-skill nodes additionally carry `skillType: "deterministic" | "agentic"`, and where applicable `supportsPhases: [phaseId, ...]` (links to existing `JOURNEY.phases`) and `linkedAgentId` (to reconcile with an existing v2 agent rather than duplicate).
   - `flows`: each `{ from, to, label }` expressing data movement (e.g. `s4 → databricks`, `kinaxis → databricks`, `databricks → demand-sensing`, `demand-sensing → kinaxis` pre-builds, `aera-skill → <phase/agent above>`).
   The engine renders the band 100% from this object — adding a node or flow must require editing data only.

2. **Render the persistent foundation band below the timeline.** A band pinned beneath the scrolling timeline lane showing the layer rows (Systems of Record → Data Platform → Planning → Aera Skills) with **upward connectors** indicating data feeding up into the phases/agents. Keep it readable across the 8-column width; horizontal scroll behavior must stay consistent with the timeline above.

3. **Badge deterministic vs agentic (badge + legend only).** Each Aera-skill node shows a small badge for its `skillType`, with distinct-but-on-brand styling. Extend the legend to explain the two badges. **Do NOT add a new filter control** for skill type (existing capability/status/refineryClass filters remain unchanged).

4. **Aera as a called-service layer.** Visually treat the Aera-skills layer as services *called by* the agents above (per the crosswalk), not as orchestrators.

5. **Detail on demand (optional, only if it aids clarity).** If a node carries a `note`, surface it on hover/click consistent with the existing drawer/expand interaction — no separate dependency, keyboard-accessible.

Keep the existing timeline, group banding, cards, drawer, and three filters fully functional.
</stage_2_build>

<constraints>
- **DO NOT EDIT `npi-timeline-framework-v2.html` or `npi-timeline-framework.html`.** v2A is a new, separate file. WHY: the "v2A" exists as a thinking artifact to diff and discuss against v2 — overwriting v2 destroys the comparison.
- **Single file, zero dependencies.** All CSS in one `<style>` block, all JS in one `<script>` block. No CDN, no Google Fonts, no icon library, no build step. Must open via `file://` fully offline. Inline SVG or unicode for every glyph — no external image files. WHY: it gets emailed and opened on locked-down client machines with no network.
- **Vanilla only.** No React/Vue/jQuery/Tailwind — plain HTML/CSS/JS so any consultant can edit it.
- **Data/engine separation preserved.** A non-coder must be able to add/edit a foundation layer, node, or flow by editing only the data objects at the top. If adding content requires touching render logic, the design has failed.
- **Aesthetic continuity.** Match v2: Accenture purple `#A100FF` primary accent, neutral grays, generous whitespace, system fonts, transitions ≤200ms. The band should read as part of the same artifact, not a bolted-on diagram.
- **Do not invent client-confidential figures.** Use the user's narrative facts only; mark unknowns "TBD". Do not fabricate data-flow edges you cannot justify from the context — list uncertain ones as Stage-1 assumptions instead.
- **Terminology:** it is **Kinaxis** (not Canaxis/Conaxis). Distinguish **skills** (Aera) from **agents** (the timeline) deliberately, per the user's framing.
</constraints>

<output>
Create exactly two files (no others):
1. `./research/npi-foundation-layer-design.md` — the Stage-1 reasoning: layered model, v2 reconciliation, 2–3 layout candidates with tradeoffs, the recommendation, deterministic/agentic encoding spec, and open questions/assumptions.
2. `./npi-timeline-framework-v2A.html` — the complete, self-contained v2A page: v2's engine plus the persistent data-driven technology-foundation band with badged Aera skills. Engine must be complete (no TODOs in render logic).
</output>

<verification>
Before declaring complete, state pass/fail for each:
1. **v2 untouched:** `npi-timeline-framework-v2.html` and `npi-timeline-framework.html` are byte-for-byte unchanged.
2. **Stage order honored:** the design doc exists and its recommendation matches what v2A implements (the band layout, the reconciliation decisions).
3. **Offline-valid:** v2A opens with no console errors and contains zero `http(s)://` asset/script/style references.
4. **Data-driven proof:** adding a hypothetical node or flow to the `FOUNDATION` object would appear in the band with no other code change; the renderer iterates `FOUNDATION.layers`/`nodes`/`flows` and reads every field used.
5. **Layers present:** the band shows S4 (systems of record) → Snowflake/Databricks (data platform) → Kinaxis (planning) → Aera skills, with upward connectors into the timeline above.
6. **Skill badges:** each Aera skill node shows a deterministic/agentic badge; the legend explains both; **no new filter control was added**; the three existing filters still work.
7. **Reconciliation:** Demand Sensing is linked to / not duplicated against v2's existing `DemandSensing`/`SignalCapture` agent(s); the doc states the decision.
8. **Aera as called service:** the Aera layer reads as a called-service layer, not an orchestrator.
9. **Timeline intact:** 8-step continuous timeline, group banding, cards, click-to-expand drawer, and drag/scroll all still function.
</verification>

<success_criteria>
- A Stage-1 design doc that genuinely *thinks through* how to layer the S4/Databricks/Kinaxis/Aera foundation beneath the NPI timeline, with real alternatives and a justified recommendation — produced before any code.
- One self-contained `./npi-timeline-framework-v2A.html` that opens offline, preserves the full v2 timeline, and adds a persistent, data-driven technology-foundation band showing data feeding up from S4/Kinaxis through Databricks into Aera skills that support NPI.
- Each Aera skill is clearly badged deterministic vs agentic (badge + legend, no new filter); Aera reads as a called service; the three named skills are reconciled against existing v2 agents, not duplicated.
- v2 (and the original master) remain untouched, so v2A can be diffed and discussed as a thinking artifact.
</success_criteria>
