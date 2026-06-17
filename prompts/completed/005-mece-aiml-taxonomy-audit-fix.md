<objective>
Audit all AI/ML taxonomy definitions in the NPI timeline HTML deliverables for MECE compliance (Mutually Exclusive, Collectively Exhaustive), then apply in-place fixes so every category set is clean. Do both analysis and remediation in a single pass.

MECE criteria:
- **Mutually Exclusive**: no two items in a category set should overlap in meaning or scope — an agent or capability should fit exactly one bucket.
- **Collectively Exhaustive**: the category set must cover the full problem space with no meaningful gaps — every real-world agent type or capability that appears in the data must have a valid home.
</objective>

<context>
This is a consulting artifact workspace (no build system). The primary deliverables are single-file HTML visualizations for a Hershey's NPI engagement. The AI/ML taxonomy sits inside the `<script>` DATA region of each file and drives all rendering — filters, badges, legend items, drawer metadata.

Key files to audit (read all before touching any):
- `./npi-timeline-framework-v2.html` — v2 deliverable; contains `REFINERY_CLASSES`, `CAPABILITIES`, `NODE_TYPES`, and all `agents[]` entries with fields `refineryClass`, `capabilities`, `orchestration.role`, `transformation.badge`
- `./npi-timeline-framework-v2B.html` — **primary audit target**; same taxonomy structure as v2 but with the people-impact band and renamed "Aera Agent Teams" layer; apply all fixes here first
- `./npi-timeline-framework.html` — base engine with generic sample data; audit `CAPABILITIES` and `NODE_TYPES` only (do not change render logic)
- `./research/npi-refinery-crosswalk.md` — the authoritative v12→AI Refinery mapping; treat as ground truth for intent; flag any divergence between it and the HTML

Do NOT modify any code in the ENGINE region (clearly commented in each file). Only touch the DATA region objects and their usage in agent entries.
</context>

<analysis_phase>
Read all three HTML files and the crosswalk. Then produce an inline audit report covering each of these category sets independently:

1. **`REFINERY_CLASSES`** (v2 only) — the `agent_class` badges: FlowSuperAgent, CustomAgent, ToolUseAgent, UtilityAgent, HumanAgent, AnalyticsAgent, ResearchAgent, A2AClientAgent, etc.
   - Are any two classes semantically overlapping (an agent could plausibly belong to either)?
   - Are there agent entries in `JOURNEY.agents[]` whose `refineryClass` value doesn't match any key in `REFINERY_CLASSES`?
   - Are there real agent patterns in the data that don't fit any existing class?

2. **`CAPABILITIES`** — the legend taxonomy (id → label + color)
   - Do any capability ids share overlapping meaning?
   - Are there agents referencing a `capabilities[]` value that doesn't exist in `CAPABILITIES`?
   - Are there agent capability needs that no existing id covers?

3. **`NODE_TYPES`** — card badge taxonomy: trigger | human | agent | gate | workflow
   - Are these five types mutually exclusive for the cards that exist?
   - Is any card type used in data that's missing from `NODE_TYPES`?

4. **`transformation.badge`** values — TRANSFORMED·AUGMENTED / ENABLED·AUTOMATED
   - Are these two values consistently applied? Any agents where the badge contradicts the `refineryClass`?

For each finding, record:
- Category set name
- Finding type: OVERLAP | GAP | INCONSISTENCY | ORPHAN
- Specific items involved
- Recommended fix (rename / merge / add / reclassify)
</analysis_phase>

<fix_phase>
After completing the audit report, apply all recommended fixes directly to the HTML files. Fix rules:

- **Rename**: update the key in the taxonomy object AND every reference to it in `JOURNEY.agents[]` and `touchpoints[]`
- **Merge**: pick the canonical name, update all references, remove the duplicate key
- **Add**: insert the new entry into the taxonomy object; reclassify the orphan agent entries that need it
- **Reclassify**: update the `refineryClass` / `capabilities[]` / `type` field on the affected agent entries only

Do not invent new agent data or alter `tooling`, `aera`, `orchestration.nodes/edges`, or drawer `description` text — only fix the taxonomy keys and the fields that reference them.

If a fix would change a visible label that appears in `research/npi-refinery-crosswalk.md`, add a one-line `<!-- MECE fix: old → new -->` HTML comment next to the changed key so a reviewer can reconcile.

Apply fixes to `npi-timeline-framework-v2B.html` first (primary target). Then propagate the same fixes to `npi-timeline-framework-v2.html`. Apply parallel fixes to `npi-timeline-framework.html` only for issues found in `CAPABILITIES` or `NODE_TYPES` that exist in all files.
</fix_phase>

<output>
1. Inline audit report (printed to stdout before touching any file) — structured as:

   ## MECE Audit: [Category Set Name]
   | Finding | Type | Items | Fix |
   |---------|------|-------|-----|
   ...
   **Status**: PASS / ISSUES FOUND (N findings)

2. Modified files:
   - `./npi-timeline-framework-v2B.html` — DATA region taxonomy objects and agent entries patched (primary)
   - `./npi-timeline-framework-v2.html` — same fixes propagated
   - `./npi-timeline-framework.html` — CAPABILITIES / NODE_TYPES patched if issues found there

3. Summary line: "Fixed N issues across K category sets. M items required reclassification."
</output>

<constraints>
- Single-file constraint: do not add any `<link>`, `<script src>`, or external reference.
- Engine region is read-only: only the DATA region (clearly delimited by comments) may change.
- Do not alter any metric figures, phase names, or agent descriptions — only taxonomy keys and the fields that point to them.
- Preserve all existing inline SVGs in the `GLYPHS` map; if a rename changes a glyph key, update both the `GLYPHS` entry and every `CAPABILITIES` reference that uses it.
- Accenture purple `#A100FF` is the primary accent; do not change any color values unless a new taxonomy entry genuinely needs a distinct color (use a neutral gray from the existing palette).
</constraints>

<verification>
Before declaring complete:
1. Open all three HTML files and confirm every `refineryClass` value in `agents[]` has a matching key in `REFINERY_CLASSES`.
2. Confirm every `capabilities[]` value in `agents[]` has a matching key in `CAPABILITIES` and a corresponding entry in `GLYPHS`.
3. Confirm no two keys in `REFINERY_CLASSES` have definitions that would make a single agent ambiguously classifiable.
4. Run a quick `grep` for any leftover references to renamed/removed keys to ensure no orphan strings remain.
5. Verify the HTML still opens and renders (no JS syntax errors) by checking for unclosed brackets or missing commas introduced by edits — read the edited sections after saving.
</verification>

<success_criteria>
- Audit report produced with explicit PASS/ISSUES FOUND per category set
- All OVERLAP, GAP, INCONSISTENCY, and ORPHAN findings resolved
- All three HTML files parse as valid JS (DATA region syntactically intact)
- No rendering engine code touched
- All taxonomy keys referenced in agent data have a definition; no dangling references remain
</success_criteria>
