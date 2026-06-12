# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A consulting-artifact workspace, not a conventional software project. It produces **self-contained, single-file HTML visualizations** of a New Product Introduction (NPI) journey for an Accenture × Hershey's (HSY) engagement. The headline deliverable is a config-driven timeline framework that re-renders a static two-page slide deck as one continuous, horizontally-scrolling, interactive timeline a consultant can present to a client offline.

There is no build system, package manager, test suite, or git repo. Artifacts are HTML files emailed to and opened on locked-down client machines.

## Running / viewing

The HTML files are designed to open directly via `file://` with zero network access. To preview with a local server (the only pre-approved command):

```bash
python3 -m http.server 8731   # then open http://localhost:8731/npi-timeline-framework-v2.html
```

`.playwright-cli/` holds Playwright snapshots/console logs from automated browser checks of the rendered pages.

## Hard constraints on every HTML artifact

These are non-negotiable and override any general instinct to reach for tooling:

- **Single file, zero dependencies.** All CSS in one `<style>` block, all JS in one `<script>` block. No CDN links, no Google Fonts, no icon libraries, no npm/build step. Must work opened via `file://` offline. Glyphs are **inline SVG or unicode only** — never external image files (see the `GLYPHS` map).
- **Vanilla only.** No React/Vue/jQuery/Tailwind. Plain HTML/CSS/JS so any consultant can edit it.
- **Data/engine separation is the entire point.** A non-coder must be able to add a phase or an agent by editing only the data objects at the top of `<script>`. If adding content requires touching render logic, the design has failed. The engine reads 100% from data in a single init pass; nothing is hardcoded in markup.
- **Never invent client-confidential figures.** Use generic placeholders ("X,000 Hours/Year") in samples; preserve real v12 metrics verbatim; mark gaps "TBD".
- **Aesthetic:** Accenture consulting style — purple `#A100FF` primary accent, neutral grays for stage bars, generous whitespace, system font stack, transitions ≤200ms.

## Architecture of the timeline framework

Two HTML deliverables share one rendering engine:

- **`npi-timeline-framework.html`** — the **master/base engine** with generic NPI sample data. Treat as read-only when building versions (prompt 003 forbids editing it so the two can be diffed).
- **`npi-timeline-framework-v2.html`** — copies the master's engine + CSS, then re-populates it with Hershey's v12 page-3 content recharacterized into the AI Refinery agent library.

Inside each file, the `<script>` is split into a clearly-commented **DATA region** (edit here) and an **ENGINE region** (renders from data; add a field to data, then read it here). Key data objects:

- `CAPABILITIES` — the legend taxonomy (id → label + color); each id needs a matching inline SVG in `GLYPHS`.
- `NODE_TYPES` — card badge taxonomy: `trigger | human | agent | gate | workflow`.
- `REFINERY_CLASSES` (v2 only) — AI Refinery `agent_class` badges (`FlowSuperAgent`, `CustomAgent`, `ToolUseAgent`, `UtilityAgent`, `HumanAgent`, `AnalyticsAgent`, `ResearchAgent`, `A2AClientAgent`, etc.) with plain-English meanings.
- `JOURNEY` — `phases` (the gray stage bars on a continuous 1..N numbered arrow; v2 adds `group` PRE-LAUNCH/LAUNCH/POST-LAUNCH banding and `fromTo`) and `agents` (cards hung under a phase via `phaseId`).
- In v2, each agent additionally carries `refineryClass`, `orchestration` (`{role:"flow-super-agent", nodes, edges}` for orchestrators or `{role:"worker", parent}` for workers), `tooling`, `transformation` ({badge, note}), and `aera`. Plus a `touchpoints` array for the 3 `HumanAgent` governance gates.

Engine responsibilities (all data-driven): continuous horizontal scroll lane + phase ruler, drag-to-pan / shift-wheel, click-to-expand drawer (keyboard-accessible: Enter opens, Escape closes), and toolbar filters by capability, status (`deployed` solid border / `planned` dashed border), and — in v2 — `refineryClass`, with "clear filters".

## The prompt pipeline (`prompts/`)

These artifacts were produced by a deliberate three-stage Claude-to-Claude pipeline; read the relevant prompt before extending a deliverable:

1. **`001-npi-agent-timeline-html.md`** → builds the master engine `npi-timeline-framework.html`.
2. **`002-npi-refinery-recharacterization-crosswalk.md`** → analyzes v12 page 3 and maps every bespoke "…Agent" onto standard AI Refinery `agent_class`es, emitting **`research/npi-refinery-crosswalk.md`**. Section 5 of that file is build-ready JSON (`phases`, `agents`, `touchpoints`) — the literal handoff to stage 3.
3. **`003-npi-timeline-v2-build.md`** → builds `npi-timeline-framework-v2.html` by folding the crosswalk into the master engine.

**Do not re-derive the v12→AI Refinery mapping** — it lives in `research/npi-refinery-crosswalk.md`. If a needed field is missing there, flag it rather than inventing it (that is stage 002's job).

## Source material (reference inputs, not deliverables)

- `hersheys_npi_planning_v12.html` / `v13.html` — the original Hershey's slide artifacts. Page 3 of v12 (the "NPI DECISION FLOW" section) is the content source for v2. Read for vocabulary and visual encoding (FROM→TO panels, ◆ decision rows, TRANSFORMED·AUGMENTED / ENABLED·AUTOMATED badges, 🔧 tooling tags, 👤 planner touchpoints, AERA band) — do not copy their markup.
- `IA_GeneralMillsJourney.pdf`, `*.png` screenshots — visual reference for the consulting style being generalized.
- AI Refinery library definitions referenced by the crosswalk live outside this repo at `/Users/vincent.wicker/Documents/Refinery/` (`demo-blueprint.md`, `research/03-distiller-service.md`). AERA is a *called* decision-intelligence service (a `ToolUseAgent`/`A2AClientAgent` node), never an orchestrator.
