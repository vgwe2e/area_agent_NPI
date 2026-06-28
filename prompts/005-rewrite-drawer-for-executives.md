<objective>
Rewrite the side-panel (drawer) content in npi-timeline-framework-v2C.html so that every section is immediately legible to an executive — not a software architect. Today the drawer renders raw developer vocabulary: camelCase agent names, DAG terminology, MILP/RAG/Monte Carlo jargon in sub-agent descriptions, and the "AI Refinery class" label. An executive opening a card should see plain-language context that answers "what does this thing do for the business and who controls it?" — not a pipeline architecture spec.
</objective>

<context>
File: /Users/vincent.wicker/Documents/area_agent_NPI/npi-timeline-framework-v2C.html

This is a self-contained HTML file (no build system, no CDN). All content is in the DATA region (lines 734–1113); the ENGINE region (lines 1234 onward) renders from data. The drawer (right-hand slide-in panel) is built in the openDrawer() function (~line 1578) and rendered entirely from the agent/touchpoint data objects.

The audience for this artifact is Hershey's supply chain executives. They understand the business problem (NPI is messy, too many touchpoints, we want agents to help) but they do not know what a DAG, RAG, MILP, magic_prompt, FlowSuperAgent, or UtilityAgent is.

Key problem sections in the drawer (in order of severity):
1. Orchestration section — "Orchestration — Pipeline it coordinates (DAG)" as a heading, camelCase agent names (LikeProductResearcher, PortfolioOptimizer…), and sub-agent descriptions written as developer specs ("RAG over the like-product performance library", "MILP + weighted scoring of SKU/channel options").
2. Worker agent text — "Worker inside DemandForecastPipeline · node DemandSensing · shared worker (declared once, reused across pipelines)" — pure developer spec.
3. AI Refinery class section header — "AI Refinery class" means nothing to executives; the meaning text inside is actually decent.
4. Tooling section — chip labels like "MILP · weighted scoring · like-product regression · Monte Carlo" are implementation details, not business context.
</context>

<requirements>
Rewrite the following parts of the ENGINE (not the data) so the rendered content is executive-readable, without changing data or structure:

1. **Orchestration heading** (openDrawer ~line 1633):
   - Change "Orchestration — Pipeline it coordinates (DAG)" → "How this agent works"
   - Change "Dynamic agent pool it routes" → "What this agent coordinates"

2. **Orchestration sub-agent rows** (the `d-pnode` rows, ~line 1621–1629):
   - The `agent_name` field (LikeProductResearcher, etc.) is a code identifier. Replace it with the `agent_description` text as the primary label (the human-readable sentence), and show the name as a secondary de-emphasized detail or remove it entirely.
   - The colored class badge (e.g., "Tool", "Narrator") can stay as a visual tag — it's small enough. But "Narrator" → the badge should already map via REFINERY_CLASSES.label, which shows "Narrator". That's fine.
   - The step-through arrow (↓) can stay.

3. **Worker agent text** (openDrawer ~line 1637–1643):
   - "Worker inside [PipelineName]" — translate the pipeline name to its plain-English phase title. Look up the phase from JOURNEY.phases using the parent pipeline name, or just omit the pipeline name and say "Part of the [phase.label] step · [nodeName in plain English if available]".
   - Remove "shared worker (declared once, reused across pipelines)" — this is an architecture detail.
   - Remove "persists to MemTree memory" — not meaningful to executives.
   - Suggested pattern: "One of the specialist tasks that the [phase label] agent coordinates · [plain description from o.nodeName if possible]"

4. **AI Refinery class section title** (openDrawer ~line 1610):
   - Change "AI Refinery class" → "Agent type"

5. **Tooling section title** (openDrawer ~line 1648):
   - Change "Tooling 🔧" → "How it calculates"
   - Keep the chip pills — they remain as detail for the technically-curious.
</requirements>

<constraints>
- This is a single-file vanilla HTML/JS artifact. No build step, no framework. Edit only the ENGINE (JavaScript) render functions. Do not touch the DATA region (agent objects, phases, REFINERY_CLASSES, etc.).
- Only change the strings / labels described above. Do not restructure layout, add sections, or change styles.
- Test by reading the final output of the drawer HTML for at least two agents: portfolio-optimization (FlowSuperAgent) and demand-sensing (CustomAgent worker).
- Preserve all existing functionality: keyboard navigation, focus trap, filter wiring, drag-to-pan.
- The file must still work via file:// with zero network requests.
</constraints>

<output>
Edit /Users/vincent.wicker/Documents/area_agent_NPI/npi-timeline-framework-v2C.html in place.
No new files. No documentation.
</output>

<verification>
After editing, trace the drawer HTML output (mentally or by reading the render code) for:
- portfolio-optimization: the orchestration section should show descriptions as primary text, not camelCase names
- signal-capture (worker): should read clearly without "DemandForecastPipeline" or "shared worker" jargon
- The section heading "AI Refinery class" should now read "Agent type"
- The tooling heading should read "How it calculates"
Confirm all five changes are applied.
</verification>
