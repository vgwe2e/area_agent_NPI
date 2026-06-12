# NPI Timeline — Technology & Data Foundation Layer (Design Reasoning)

> **Purpose:** Work out *how* to visualize the technology/data substrate beneath the NPI timeline — SAP S4, Snowflake/Databricks, Kinaxis, and Aera Technologies — and how data captured from S4 and Kinaxis feeds **up** into the Aera skills that support New Product Introduction. This doc is the Stage-1 thinking behind `npi-timeline-framework-v2A.html`. The master `npi-timeline-framework-v2.html` is **not** edited.
>
> **Decisions already fixed (by the user):** persistent band **below** the timeline · deterministic-vs-agentic = **badge + legend only, no new filter**.

---

## 1. The layered model

The user's narrative maps onto four capability tiers. Rendered top→bottom (top = closest to the timeline it feeds):

| Tier | Layer | Nodes | Role |
|---|---|---|---|
| 1 (top) | **Aera Skills** | Demand Sensing · On-Shelf Availability · Manufacturing Frozen-Period Cut | Decision-intelligence skills — a *called* service layer (consistent with the crosswalk's stance that AERA is a called `ToolUseAgent`, never an orchestrator). |
| 2 | **Planning** | Kinaxis | Demand planning (incl. NPI launch inputs) + supply planning; receives inventory pre-builds / production cuts written back by the Aera skills. |
| 3 | **Data Platform** | Snowflake / Databricks | Consolidated lakehouse — captures transactional data from S4 and Kinaxis; the single source the skills read. |
| 4 (bottom) | **Systems of Record** | SAP S/4HANA | FG planning, RM planning, all master data. |

**Why this order:** the dominant story is "data originates at the bottom and feeds up into skills that support the journey." Putting Systems of Record at the bottom and Aera Skills at the top (adjacent to the timeline) makes the primary flow read upward, which matches the user's "feeds **up** into the skills" framing.

**The flow is not perfectly linear** — Kinaxis both *emits* data (captured by Databricks) and *receives* writeback (pre-builds/cuts) from Aera. Rather than distort the tiers, the layers are treated as **capability tiers** and the real edges live in a `flows` array. Primary upward flows render as bold connectors; the Aera→Kinaxis writeback renders as a muted return (↩) connector at the same boundary.

Primary flows: `S4 → Databricks` (master data, FG/RM plans, transactions) → `Databricks → Kinaxis` (data backbone feeds planning) → `Kinaxis → Aera skills` (plans + signals feed the skills). Return: `Aera skills → Kinaxis` (supply plans · inventory pre-builds · production cuts).

---

## 2. Reconciliation with v2 (avoid double-representation)

v2 already contains agents derived from the crosswalk. Each Aera skill is checked against them:

| Aera skill | `skillType` | Existing v2 agent? | Decision |
|---|---|---|---|
| **Demand Sensing** | deterministic | **Yes** — `demand-sensing` (Demand Sensing Agent, `CustomAgent`) + `signal-capture` (Signal Capture Agent, `ToolUseAgent`), both workers in steps 2 & 7. | **Link, don't duplicate.** The Aera "Demand Sensing" skill is the underlying platform capability those two timeline workers embody (POS→cases, sell-through curves, recalibration). Node carries `linkedAgentId: "demand-sensing"`; the drawer says "appears in the timeline as …, shown here once as the underlying skill." |
| **On-Shelf Availability (OSA)** | deterministic | **No direct agent.** Relates to the "shelf availability" signal in Signal Capture (step 7) and step 6's placeholder ("real-time tracking of shelf availability at each account"). | **New node.** `supportsPhases: ["distribution-fulfillment","launch-execution-adjustments"]` (steps 6 & 7). No `linkedAgentId`. |
| **Manufacturing Frozen-Period Cut** | deterministic | **No direct agent.** Relates to step 4 (production schedule/sequencing — placeholder) and the step-7 Lifecycle Management Agent ("adjust production… in near real-time"). | **New node.** `supportsPhases: ["downstream-planning-systems","launch-execution-adjustments"]` (steps 4 & 7). Drives supply planning & pre-builds in Kinaxis. |

**AERA reconciliation:** the timeline already shows AERA Decision Cloud as a *called service* marker on phases. The foundation band's **Aera Skills layer is that same called service, expanded into its named skills** — the band shows *what Aera contains*; the timeline shows *where Aera is called*. They are consistent, not contradictory. The band reaffirms Aera-as-called-service (it sits below and feeds up; it does not orchestrate).

---

## 3. Alignment to the timeline

The band is a **global substrate**, not 8 per-phase columns. SAP S4 / Databricks / Kinaxis underlie *every* phase, so column-aligning them to the 8 steps would be misleading and (because the timeline scrolls horizontally while the band does not) impossible to keep physically aligned.

Instead: each **Aera skill node carries `supportsPhases` chips** ("→ Step 2", "→ Step 7") that are **clickable and scroll the timeline to that phase**. This expresses "feeds up into *these* steps" precisely, without fragile arrow-to-column geometry. An apex strip ("↑ Feeds the NPI journey above") spans the Aera layer to convey the upward direction into the timeline as a whole.

---

## 4. Candidate layouts considered

**A — Global fixed layered band + per-skill support-step chips + click-to-expand notes (RECOMMENDED).**
Four stacked layer rows below the timeline (not scroll-synced). Bold upward connectors between adjacent rows; muted writeback connector. Aera skills carry deterministic/agentic badges, consumes/writes chips, and clickable step chips. Each node opens its detail in the existing drawer.
*Pros:* reads as one honest picture of a global substrate; no scroll-alignment trap; reuses the drawer; fully data-driven. *Cons:* "feeds up into step N" is via chips, not a literal drawn arrow into the column.

**B — Scroll-synced substrate columns.** Band scrolls in lockstep with the timeline (like the group band's `translateX`), Aera skills positioned physically under the steps they support.
*Pros:* literal per-column "feeds up here." *Cons:* S4/Databricks/Kinaxis are global and would have to stretch full width or repeat under every column → clutter and empty cells; skills cluster under a few steps leaving big gaps. Rejected.

**C — Band + click-to-expand architecture diagram.** Compact one-line band; full stacked diagram opens in the drawer.
*Pros:* minimal default footprint. *Cons:* hides the substrate by default — defeats the goal of seeing the foundation *while* scrolling the journey. Rejected as the primary, but its drawer-detail idea is folded into A (clicking a node opens its note/flows/steps).

**Recommendation: A.** It honors the fixed "persistent band below the timeline" direction, keeps the substrate legible as a whole, and avoids the horizontal-scroll alignment problem while still linking each skill to the steps it supports.

---

## 5. Deterministic vs agentic encoding (badge + legend only)

- New `skillTypes` taxonomy in data: `deterministic` (teal `#0F9D8C`, the existing "logic" primitive) and `agentic` (Accenture purple `#A100FF`).
- Each Aera skill node shows a small uppercase `skill-badge` for its type. The legend gains an **"Aera skill type"** section explaining both.
- All three current skills are **deterministic** — but the `agentic` badge is fully designed so a future agentic skill (just set `skillType: "agentic"`) renders correctly with no code change.
- **No new filter control** is added (the three existing filters — capability, status/class — are untouched), per the fixed decision.

---

## 6. Open questions / assumptions (please confirm or correct)

1. **Layer order** — assumed `S4 → Databricks → Kinaxis → Aera`. If Kinaxis reads directly from S4 (not via Databricks), the `Databricks → Kinaxis` edge should become `S4 → Kinaxis` + `Kinaxis → Databricks` (capture). Left as the linear stack for clarity; lateral `S4 ↔ Kinaxis` was intentionally omitted to reduce clutter.
2. **OSA data path** — assumed OSA reads distributor/customer shelf & warehouse data *via* the Databricks platform. If it calls customer systems directly (A2A), that is a separate edge worth adding.
3. **Demand Sensing identity** — assumed the Aera Demand Sensing skill == the capability behind v2's `demand-sensing` + `signal-capture` workers. If they are meant to be distinct, the link should be removed.
4. **Frozen-Period Cut placement** — mapped to steps 4 & 7. If it belongs to a supply/manufacturing step not yet on the 8-step flow, that step may need adding to `JOURNEY.phases` first.
5. **"Skills" vs "agents" terminology** — kept deliberate: the band labels them Aera *skills*; the timeline labels its nodes *agents*. The band is the called-service substrate; the timeline is the orchestrated journey.
