<objective>
Add a "10K-Foot Overview" tab to `npi-timeline-framework-v2F.html`, producing `npi-timeline-framework-v2G.html`.

The problem: Sunil and others feel immediately lost when the detailed timeline is the first thing they see. They need a single-screen orientation card — what problem we're solving, the agent chain at a glance, and the value delivered — before the step-by-step detail. This overview tab is the landing view; the existing timeline becomes Tab 2 ("Detail").

The reference design (described below) is the "Spare Parts Planning Command Center" 1-pager used on a comparable Accenture engagement. Adapt its layout and visual language to the Hershey's NPI context.
</objective>

<context>
Working file: `./npi-timeline-framework-v2F.html` (2 873 lines)
Output file:  `./npi-timeline-framework-v2G.html`

Hard constraints (from CLAUDE.md — non-negotiable):
- Single file, zero dependencies. All CSS in one `<style>` block, all JS in one `<script>` block. No CDN links, no Google Fonts, no icon libraries. Works via `file://` offline.
- Vanilla HTML/CSS/JS only. No React, Vue, jQuery, Tailwind.
- Data/engine separation. Any new data for the overview (signals, outcomes, macro-phases) must live in a dedicated const at the top of the DATA region — not hardcoded in markup or in render logic.
- Accenture consulting aesthetic: `#A100FF` purple primary, neutral grays, generous whitespace, system font stack, transitions ≤200ms.
- Glyphs are inline SVG or unicode only.

Implementation approach:
- Copy v2F in full. Add a two-tab chrome (Tab 1 = "Overview", Tab 2 = "Detail") to the existing header or just below it. Tab 2 renders exactly what v2F renders today, unchanged. Tab 1 is the new overview screen.
- The tab switcher should feel native to the existing header style — minimal, no heavy chrome.
- Tab 1 must fit a single viewport (no vertical scroll) — same constraint as the existing timeline (one-screen layout).
- The SHA-256 password gate in v2F must be preserved exactly; the overview tab sits behind it too.
</context>

<overview_tab_layout>
Model this on the "Spare Parts Planning Command Center" reference card. Translate each region to the NPI/HSY context:

┌─────────────────────────────────────────────────────────────────────────────────┐
│  > NPI Decision Flow Command Center                                             │
│    Agentic AI for new product introduction across the Hershey's supply chain   │
│                                                                                 │
│  One-sentence problem statement + one-sentence "what changes" statement         │
│                                                                                 │
│  ┌──────────────────┐   ┌────────────────────────────────────────┐   ┌────────┐│
│  │  BUSINESS NEED   │   │  THE AGENT CHAIN — 8 decisions         │   │ VALUE  ││
│  │                  │   │                                        │   │  OUT   ││
│  │  NPI triggers:   │   │  ①②③  H1  ④⑤  H2  ⑥  H3             │   │        ││
│  │  • Portfolio     │   │  PRE-LAUNCH  →  LAUNCH  →  POST-LAUNCH│   │ 3 kpis ││
│  │    review        │   │                                        │   │        ││
│  │  • Demand signal │   │  ● Automated agent (5)                 │   │        ││
│  │  • Launch window │   │  ● Human checkpoint (3)                │   │        ││
│  │  • Market event  │   │                                        │   │        ││
│  └──────────────────┘   └────────────────────────────────────────┘   └────────┘│
│                                                                                 │
│  ┌──────────────────────┐  ┌──────────────────────┐  ┌──────────────────────┐  │
│  │  1  BUILD THE PLAN   │  │  2  EXECUTE & ADJUST  │  │  3  LEARN & SCALE   │  │
│  │  Scores SKUs, builds │  │  Positions inventory, │  │  Feeds every outcome │  │
│  │  forecast, sizes the │  │  resolves exceptions  │  │  back so the next NPI│  │
│  │  pre-build — one     │  │  in hours, not days.  │  │  starts smarter.     │  │
│  │  planner session.    │  │                       │  │                      │  │
│  └──────────────────────┘  └──────────────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────────┘

Exact NPI content to encode (use these verbatim; do not invent or soften):

PROBLEM_STATEMENT (headline card, 1-2 sentences):
  "Today an NPI launch requires 25+ planner touchpoints, a static one-time forecast, and manual handoffs across four teams. The result: overbuild write-offs, missed launch-week fill rates, and lessons that never reach the next launch."

WITH_THIS_SYSTEM (1-2 sentences):
  "The NPI Decision Flow collapses those 25+ touchpoints to 3 structured planner approvals — automating the routine, keeping planners in control of the decisions that matter."

SIGNALS_IN (left panel, title "BUSINESS NEED"):
  Label: "NPI launch triggers"
  Items:
  - { icon: "◆", label: "Portfolio Review", sub: "Brand teams stage new SKU candidates" }
  - { icon: "◆", label: "Demand Signal", sub: "POS / EDI data diverges from plan" }
  - { icon: "◆", label: "Launch Window", sub: "Calendar milestone triggers supply action" }
  - { icon: "◆", label: "Market Exception", sub: "In-flight deviation requiring a decision" }

AGENT_CHAIN_STEPS (middle panel):
  Draw 8 numbered circles on a continuous arrow, coloured by group:
    PRE-LAUNCH (red #E2231A): steps 1, 2, 3, 4
    LAUNCH (purple #7500C0): steps 5, 6, 7
    POST-LAUNCH (teal #0F9D8C): step 8
  Human checkpoints (blue #2563EB, teal fill):
    H1 after step 3 (label: "Plan Approval")
    H2 after step 6 (label: "Exception Review")
    H3 after step 7 (label: "Scale Decision")
  Legend below the chain:
    ● Automated agent (5)   ● Human checkpoint (3) — planner decides & signs off
  Note under legend (smaller italic): "25+ planner touchpoints today → 3 structured approvals"

VALUE_OUT (right panel, title "COMMITTED OUTCOME"):
  - { icon: "◎", label: "Right product, right place", sub: "SKU decisions grounded in analogue evidence, not gut feel" }
  - { icon: "⚡", label: "Hours, not days", sub: "Exceptions resolved before launch velocity is lost" }
  - { icon: "⟳", label: "Every launch improves the next", sub: "Like-product library updated automatically at cycle close" }

MACRO_PHASES (3 bottom cards, match the group bands in the timeline):
  1. BUILD THE PLAN (PRE-LAUNCH, red accent)
     "Scores the SKU portfolio, builds a live demand curve, and sizes the pre-build — all resolved in one planner approval session before inventory commits."
  2. EXECUTE & ADJUST (LAUNCH, purple accent)
     "Positions stock where demand is developing, fulfils via SAP, and resolves in-flight exceptions in hours — with a second planner gate for the decisions that matter."
  3. LEARN & SCALE (POST-LAUNCH, teal accent)
     "Compares actuals to every forecast and inventory decision, updates the like-product library, and recalibrates model weights — automatically, at cycle close."
</overview_tab_layout>

<implementation>
1. Copy v2F verbatim into v2G. Do not alter any existing HTML, CSS, or JS outside of the specific additions below.

2. In the DATA region (top of `<script>`), add a new const `OVERVIEW` containing all the strings from `<overview_tab_layout>` above. Use the exact structure needed by the render logic you will write — labels, subs, icons, phase descriptions. Nothing in OVERVIEW should be hardcoded in the render function.

3. Tab chrome — add just below the `<header class="app">` element:
   ```html
   <nav class="tab-bar" role="tablist" aria-label="View">
     <button class="tab active" role="tab" aria-selected="true"  aria-controls="pane-overview" id="tab-overview">Overview</button>
     <button class="tab"        role="tab" aria-selected="false" aria-controls="pane-detail"   id="tab-detail">Detail →</button>
   </nav>
   ```
   Style `.tab-bar`: slim, sits flush under the header, uses the existing `var(--hair)` border, `var(--bg)` background, no extra height. Active tab has `var(--accent)` bottom border and bold weight; inactive tabs are muted.

4. Wrap the existing `.stage` div in `<div id="pane-detail" role="tabpanel" ...>`. Add `<div id="pane-overview" role="tabpanel" ...>` before it.

5. Tab switching JS: add a `switchTab(id)` function that toggles `hidden` on the two pane divs and updates `aria-selected` on the two tab buttons. Bind click on each tab button.

6. On load, the overview pane is shown first (`pane-detail` starts hidden). The password gate (if not yet unlocked) should block both panes — existing gate logic is unchanged.

7. Overview render function `renderOverview()`:
   - Reads 100% from `OVERVIEW` const.
   - Renders the three-column card row (BUSINESS NEED | AGENT CHAIN | COMMITTED OUTCOME).
   - Renders the three macro-phase cards at the bottom.
   - The agent chain circles must be drawn with inline SVG (or HTML+CSS flex — no canvas) so it prints cleanly.
   - The entire overview must fit within `100dvh - header height - tab-bar height` without vertical scroll on a 1280×800 or larger screen. Use `flex: 1 1 auto` and careful padding to stay within bounds.
   - Match the existing card/panel aesthetic: `var(--shadow)`, `var(--radius)`, same font stack.

8. Do NOT alter the existing rendering engine, data objects (CAPABILITIES, REFINERY_CLASSES, JOURNEY), or any CSS that the timeline uses — only add to them.

9. The tab bar must be hidden when the password gate overlay is visible (gate hides or covers everything underneath as it does today).
</implementation>

<output>
Save the completed file as: `./npi-timeline-framework-v2G.html`

Do not create any other files.
</output>

<verification>
Before declaring complete:
1. Open via `python3 -m http.server 8731` and navigate to `http://localhost:8731/npi-timeline-framework-v2G.html`.
2. Confirm the password gate appears first.
3. After entering the correct password, confirm the Overview tab is shown by default.
4. Verify all OVERVIEW data renders correctly — signals, chain steps with correct colours and H-gate positions, value outcomes, macro-phase descriptions.
5. Click "Detail →" and confirm the existing timeline renders exactly as in v2F — all agents, phases, filters, drawers, keypane.
6. Switch back to Overview — confirm state is stable.
7. Check at ~1280×800 viewport: Overview must fit without vertical scroll.
8. Check that no CDN links, external fonts, or external images were introduced.
</verification>

<success_criteria>
- `npi-timeline-framework-v2G.html` is a single self-contained file.
- Overview tab loads as the default view, presents a clear narrative arc: problem → agent chain → value.
- Detail tab is pixel-identical to v2F's existing timeline.
- All OVERVIEW data is sourced from a single `const OVERVIEW` in the DATA region.
- No CDN, no build step, works `file://` offline.
- Password gate behaviour is unchanged.
</success_criteria>
