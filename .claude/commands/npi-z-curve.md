---
name: npi-z-curve
description: Generate a Z-curve racetrack process flow for the NPI/Hershey's engagement. Pre-seeded with Accenture style, type taxonomy, and project constraints. Pass step labels or a JSON steps array as arguments.
args:
  - name: spec
    description: Steps to render — comma-separated labels, JSON array of step objects, or a description of what the flow covers.
    required: false
---

Run the z-curve-flow skill with NPI project context pre-applied:

- Output directory: current working directory (same folder as the other NPI HTML files)
- Design tokens: Accenture purple `#A100FF`, dark spine `#4A00B4`, background `#FAFAFA`, system font stack
- Type taxonomy: data | transform | human | boundary | decision (colors as defined in s-curve-experiment.html)
- Constraints: single file, zero dependencies, vanilla JS, file:// compatible, `document.createElementNS` for all SVG nodes
- DATA / ENGINE delimiter convention: `// ─── DATA ───` and `// ─── ENGINE ───` exactly
- Default output filename pattern: `npi-[flow-name]-flow.html`

Steps spec from user: $ARGUMENTS

Invoke: Skill(z-curve-flow) with the above context merged into the spec.
