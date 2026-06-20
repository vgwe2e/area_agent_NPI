<objective>
Build a self-contained HTML experiment that renders a dynamic "S-curve" / "Z-curve" process flow diagram — the kind consultants use to show a multi-step sequence snaking from start to finish. The diagram must be data-driven: given an array of step objects (each with a label, icon, and optional type tag) it automatically computes node positions, lays them out along the curve, and renders them with SVG. The goal is to validate this as a viable visualization primitive before wiring it into the existing NPI timeline drawer panels.
</objective>

<context>
Project: `npi-timeline-framework` — single-file, zero-dependency HTML artifacts for an Accenture × Hershey's consulting engagement.

Constraints from CLAUDE.md (non-negotiable):
- Single file, zero dependencies. No CDN, no npm, no external fonts or images.
- Vanilla HTML/CSS/JS only.
- All glyphs are inline SVG or unicode.
- Must open via `file://` with no network access.
- Accenture aesthetic: primary accent `#A100FF`, neutral grays, generous whitespace, system font stack.

The curve style: imagine a wide S (or mirrored S) or alternating Z path drawn across the canvas. Nodes (numbered circles) sit ON the path at evenly-spaced positions. Connecting arrows or the path itself acts as the spine. Labels hang above or below each node. The overall shape should feel like a consultant's "swim-lane snake" — left-to-right on row 1, right-to-left on row 2, left-to-right on row 3, etc. — so any number of steps from 1 to ~12 fits without overlapping.

The experiment output is a NEW file — do not touch any existing NPI timeline file. The file will be used to explore layout and style before integration decisions are made.
</context>

<requirements>
1. **Data structure at top of `<script>` (edit-zone only):**
   ```js
   const FLOW = {
     title: "Agent Execution Flow",
     steps: [
       { id: 1, label: "Ingest Raw Data",     type: "data",    icon: "⬇", note: "Pulls from SAP & PIM feeds" },
       { id: 2, label: "Validate & Cleanse",  type: "transform", icon: "⚙", note: "Schema checks, dedup" },
       { id: 3, label: "Enrich & Score",      type: "transform", icon: "⚙", note: "AI scoring model applied" },
       { id: 4, label: "Human Review Gate",   type: "human",   icon: "👤", note: "Planner approves or rejects" },
       { id: 5, label: "Broadcast to Systems",type: "boundary",icon: "↗", note: "Pushes to downstream APIs" },
       { id: 6, label: "Confirm & Archive",   type: "data",    icon: "✓", note: "Audit log written" }
     ]
   };
   ```
   Step types: `data` | `transform` | `human` | `boundary` | `decision` — each gets a distinct accent color.

2. **SVG curve layout engine (in `<script>` engine section):**
   - Computes the snake path: rows of nodes left→right, right→left, alternating. Row capacity = `Math.ceil(Math.sqrt(N))` nodes, so layout adjusts for any N.
   - Draws the spine as a smooth SVG `<path>` using cubic Bézier curves connecting row endpoints — the S-shape emerges from the alternating direction.
   - Places a numbered circle at each node position on the path (filled with the type's accent color).
   - Draws step labels (`label` above the node, `note` in smaller text below).
   - Draws small directional arrows (chevrons ›) along the path spine between nodes to indicate flow direction.
   - Node number badge overlays the circle center (white numeral on colored background).

3. **Three rendered experiments on the same page** (one per section with a `<h2>` heading), each using `FLOW.steps` but with a different visual variant:
   - **Variant A — Classic Snake**: nodes in alternating horizontal rows, path curves between rows at the ends.
   - **Variant B — Diagonal S**: nodes arranged along a true S-shaped Bézier, evenly distributed by arc-length approximation (can be a simpler equal-t distribution).
   - **Variant C — Compact Z**: three columns, nodes stacked vertically in each column, Z-connector between column tops/bottoms.

4. **Tooltip on hover** (CSS-only or minimal JS): hovering a node reveals a floating card with `label`, `type` badge, and `note`.

5. **Dynamic resize**: all three variants recompute layout when the page width changes (use `ResizeObserver` or `window.addEventListener('resize', …)`).

6. **Visual style**: match the Accenture consulting aesthetic.
   - Background: `#FAFAFA`
   - Node accent colors by type: data=`#A100FF`, transform=`#7B00CC`, human=`#00A3E0`, boundary=`#00875A`, decision=`#FF6900`
   - Spine stroke: `#D0D0D0`, 2px, with subtle drop shadow on nodes
   - Font: system font stack; labels 12–13px, notes 10–11px in `#666`
</requirements>

<implementation>
- SVG viewBox must be responsive — use `viewBox` + `width="100%"` so the diagram scales to the container.
- Keep the three variants in clearly separated `<section>` elements; each section has its own `<svg>` tag rendered by a dedicated function (`renderSnake(svg, steps)`, `renderSCurve(svg, steps)`, `renderZCurve(svg, steps)`).
- Shared utilities (node color lookup, tooltip construction, bezier helpers) go in a `UTILS` object above the render functions.
- Do NOT use `innerHTML` injection of user data — build SVG nodes with `document.createElementNS` to keep XSS risk zero.
- The DATA region (FLOW constant) must be clearly delimited with `// ─── DATA ───` and `// ─── ENGINE ───` comments exactly as in the existing NPI files.
</implementation>

<output>
Create a single new file at:
  `./s-curve-experiment.html`

The file must be fully self-contained and open correctly via `file://` with no console errors. Include a sticky top bar showing the experiment title and today's date (2026-06-20).
</output>

<verification>
Before declaring complete:
1. Open `./s-curve-experiment.html` via `python3 -m http.server 8731` and confirm all three variants render without console errors.
2. Resize the browser window and confirm the diagrams reflow.
3. Hover over at least one node in each variant and confirm the tooltip appears with correct content.
4. Confirm the DATA region (FLOW constant) is the only thing a non-coder would need to edit to change the steps.
5. Confirm no external URLs, no CDN links, no import statements exist in the file.
</verification>

<success_criteria>
- Three visually distinct S/Z-curve variants render from the same `FLOW.steps` data array.
- Adding or removing a step object from `FLOW.steps` automatically adjusts layout, numbering, and path — no engine edits required.
- The file passes the zero-dependency constraint: `grep -E 'http|import|require|cdn' s-curve-experiment.html` returns nothing meaningful.
- The design looks professional enough to screenshot for a consulting deck.
</success_criteria>
