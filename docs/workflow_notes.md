
# workflow_notes.md  
[AGENTARIUM_ASSET]  
Name: Orion Detective Agent — Workflow Notes  
Version: v1.1  
Status: Draft  

---

## 1. Purpose of This Document

These notes describe **how Orion Detective Agent actually works in practice**:

- Its reasoning cycle  
- How it uses memory  
- How it cooperates with the dataset suite  
- How the **Master Grid** acts as a vectorized knowledge graph  
- How builders can plug Orion into **any LLM or agent platform** safely  

This is for AI builders and integrators who want to deploy or extend Orion without guessing how the internals fit together.

---

## 2. Orion’s Role in the Agentarium Ecosystem

Orion is an **Adaptive Hybrid Detective Agent**:

- Analytical core (structured logic, contradictions, hypotheses)  
- Narrative layer (story & noir flavor, when enabled)  
- Personality fingerprint (tone, interaction style)  
- Guardrails (fictional, non-operational, non-accusatory)  
- Memory (episodic cases, user profile, reflection logs)  
- Dataset-driven pattern library + optional **Master Grid**  

Orion is **platform-agnostic** by design.  
Everything here can be used with:

> Any LLM (OpenAI, Anthropic, Google, local models) and any agent builder (custom code, LangChain, LlamaIndex, LangGraph, n8n, Make, Flowise, etc.).

There are **no hard dependencies** on a specific framework.

---

## 3. High-Level Reasoning Workflow

Every Orion run follows the same conceptual pipeline:

1. **Context Assembly**  
   - User input or case description  
   - Optional retrieved memory snapshot for that `case_id`  
   - Optional Master Grid snippets (if a vector KG is used)  

2. **Evidence Structuring**  
   - Tag statements as Facts (F), Assumptions (A), Inferences (I)  
   - Normalize into short, structured bullet points  

3. **Contradiction Scan**  
   - Apply contradiction taxonomy: TMP, LOC, CAP, ALI, CAU, QTY, ID  
   - Use contradiction pattern datasets to recognize typical shapes  

4. **Hypothesis Laddering**  
   - Generate multiple hypotheses using motive & case archetype data  
   - Rank by compatibility with facts, reliability, and contradictions  
   - Keep uncertainty explicit  

5. **Red Thread Construction**  
   - Build a minimal causal spine linking key events, choices, artifacts  

6. **Timeline Coherence Check**  
   - Use scenario/timeline templates to examine ordering and feasibility  

7. **Mode-Specific Output**  
   - `/analyze` → structured report  
   - `/reconstruct` → timeline map  
   - `/story` → narrative retelling (noir optional)  
   - `/interrogate` → sequence of gentle, clarifying questions  

8. **Optional Memory Save**  
   - Write a compact snapshot to episodic memory  
   - Optionally write a reflection log entry when the case closes  

---

## 4. Memory Integration

Orion’s memory system is **simple, explicit, and schema-driven**.  
Schemas are shipped as **header-only CSVs + example CSVs**.

### 4.1 Episodic Case Memory

File: `episodic_case_memory_schema.csv`  

Stores one row per snapshot of a case:

- `case_id`  
- brief case description  
- top facts summary  
- contradictions summary  
- leading + alternative hypotheses  
- next tests or questions  
- provisional confidence  

**Save** when:  
- an analysis step finishes,  
- a major update happens, or  
- the user pauses / closes a case.

**Retrieve** when:  
- the user wants to resume a case, or  
- a workflow needs continuity across calls.

The agent then **rehydrates** its reasoning from this compact snapshot instead of raw transcripts.

---

### 4.2 User Profile Memory

File: `user_profile_schema.csv`  

Keeps **non-PII preferences**:

- preferred mode (analyze / story / reconstruct / mixed)  
- detail level  
- noir flavor intensity  
- hypothesis depth  
- language preference  
- safety tolerance  

Used to adjust:

- structure (short vs detailed)  
- tone (plain vs noir)  
- number of alternatives considered  

---

### 4.3 Reflection Log Memory

File: `reflection_log_schema.csv`  

Stores small meta-notes **after a case**:

- what worked  
- what was uncertain  
- missed or late insights  
- one improvement idea  
- how confidence matched reality  

Used for:

- QA dashboards  
- iteration on datasets / reasoning template  
- identifying recurring blind spots  

---

## 5. Dataset Suite & How It’s Used

Orion ships with (or expects) a **suite of synthetic CSV datasets**.  
Your current folder includes examples such as:

- `case_archetypes_merged.csv`  
- `behavioral_anomalies.csv`  
- `motive_patterns.csv`  
- `contradiction_patterns.csv` / `contradictionsV2_patterns.csv`  
- `perception_witness_reliability_matrix.csv`  
- `interrogation_dataset.csv`  
- `scenario_timelines_patterns.csv`  
- `forensics.csv` and other fictional forensic pattern matrices  
- `trace_evidence_matrix.csv`  
- `for_evidence_probability_mtrx.csv`  
- `for_evidence_contamination.csv`  
- `frnsc_evidence_pattern_mtrx.csv`  
- `frnsc_reasoning_fallacies_mtrx.csv`  
- `microscene_reconstruction.csv`  
- `sensory_anom_interr.csv`, `motivational_anom_interr.csv`, etc.

> All forensic-sounding datasets are **fictional pattern templates only**.  
> They MUST NOT encode real-world crime instructions or operational procedures.

### 5.1 How Orion uses these datasets (conceptually)

- **Case archetypes** → typical narrative structures for hypothesis scaffolding  
- **Motive patterns** → common surface/hidden motive pairs and triggers  
- **Contradiction patterns** → standard shapes of TMP/LOC/ALI/etc. conflicts  
- **Witness perception & sensory matrices** → reliability scoring and failure modes  
- **Behavioral anomalies** → benign explanations for odd behavior; reduce over-interpretation  
- **Timeline patterns** → reusable structures for reconstructing sequences  
- **Fictional forensic/evidence matrices** → high-level logic (presence/absence, contamination risk, pattern vs. coincidence), always abstract and non-operational  
- **Interrogation datasets** → curated question templates and escalation paths within ethical boundaries  

These datasets can be:

- read conceptually in the system prompt / RAG pre-context, or  
- compiled into the **Master Grid** (see next section) for vector retrieval.

---

## 6. Master Grid — Vectorized Knowledge Graph

The **Master Grid** is an **optional but powerful component**:

> A *vectorized knowledge graph* built from Orion’s datasets and core detective knowledge, designed to enhance **intra-dataset reasoning and emergent patterns**.

### 6.1 What the Master Grid is

- A unified embedding space for:  
  - case archetype rows  
  - motive structures  
  - contradiction + timeline patterns  
  - witness reliability and behavioral anomalies  
  - high-level (fictional) forensic pattern rows  
- Each row becomes a **node** with:  
  - text description  
  - tags (e.g., TMP, ALI, MOTIVE_CATEGORY)  
  - confidence fields  
  - links/relationships expressed as metadata or fields  

This can be implemented in **any** vector store (Pinecone, pgvector, Chroma, Weaviate, etc.) or even in-memory — Agentarium does not prescribe a vendor.

### 6.2 How Orion uses the Master Grid

At runtime, a builder may instruct the orchestration layer to:

1. Take the user’s case description + current facts/contradictions  
2. Run an embedding search over the Master Grid  
3. Retrieve a small bundle of **most related pattern rows**  
4. Inject those rows into the LLM context as **“reference patterns”**  

Orion then uses these patterns to:

- recognize rare contradiction combinations  
- suggest nuanced motives  
- propose richer timelines  
- avoid repetitive or trivial analyses  

All while staying within fictional, non-operational bounds.

### 6.3 Safety Note

Even with a Master Grid:

- Do not encode real cases or real investigative manuals  
- Treat all forensic-labelled entries as abstract logical templates  
- Keep explanations descriptive, not prescriptive (“what this means”, not “how to do it”)  

---

## 7. Modes of Operation (Recap)

Orion supports four primary modes, which any platform can trigger via simple control tokens or flags.

### `/analyze`
- Full structured detective analysis.  
- Uses facts → contradictions → hypotheses → red thread → timeline.

### `/reconstruct`
- Focuses on ordering events and identifying temporal gaps or impossibilities.  

### `/story`
- Builds a narrative retelling of the case.  
- Uses case archetypes + motives + personality fingerprint (with optional noir).  

### `/interrogate`
- Generates short, ethical, clarifying questions.  
- Uses interrogation + behavioral anomaly patterns; respects guardrails.

Builders can expose these as:

- explicit slash-commands in a chat UI  
- buttons in a front-end  
- internal flags in an agent framework  

---

## 8. Platform Compatibility & Integration

Orion is **not tied** to any specific implementation.

A typical integration loop, regardless of stack, is:

1. **Pre-step (Orchestration Layer)**  
   - Identify `case_id` and `user_id`  
   - Retrieve latest episodic snapshot (if any)  
   - Retrieve user profile (if any)  
   - Optionally query the Master Grid for 3–10 related patterns  

2. **LLM Call**  
   - System prompt = Orion system prompt + core guardrails  
   - Context =  
     - case description / user message  
     - episodic snapshot (summarized fields)  
     - relevant datasets / Master Grid snippets (if used)  
     - user profile preferences  

3. **Post-step (Orchestration Layer)**  
   - Parse Orion’s structured output  
   - Save episodic memory snapshot  
   - Optionally append a reflection log entry  
   - Show the user the final report, story, timeline, or questions  

This pattern can be implemented in **any language, any workflow engine, any agent framework**.

---

## 9. Safety, Limits, and Non-Goals

Orion is **not**:

- a real criminal investigator  
- a legal advisor  
- a law-enforcement assistant  
- a source of operational forensic methods  

It:

- never assigns real-world guilt  
- never diagnoses real psychological conditions  
- never instructs on how to commit or hide wrongdoing  
- keeps everything either fictional or abstracted  

Guardrails must remain enabled at all times.

---

## 10. Recommended Builder Practices

To keep Orion performant and safe:

- Use the **header-only schemas** for real storage; use the **example CSVs** just as references.  
- Keep the dataset suite synthetic and non-operational as it scales.  
- Periodically review reflection logs to identify:  
  - recurring uncertainty zones  
  - missing pattern types  
  - potential new datasets or Master Grid nodes  
- Version your datasets (v1, v1.1, etc.) and note changes in your own changelog.  
- If adding tool use in a future Orion version, document it as a separate `/tools` section rather than modifying this v1 workflow.

---

## 11. What’s Left for Builders

This document, plus:

- `/meta/agent_manifest.json`  
- `/core/system_prompt.md`  
- `/core/reasoning_template.md`  
- `/core/personality_fingerprint.md`  
- `/memory_schemas/*.csv` (schemas + examples)  
- `/guardrails/guardrails.md`  
- `/datasets/*.csv` + optional Master Grid  

gives you a **complete, platform-neutral blueprint** for running Orion.

What you **add on top** (UI, orchestration flows, vector store choice, tool integrations) is fully up to your stack and imagination.

---
## 12. Quickstart (Platform-Agnostic)
1) **Assemble Context:** user message + latest episodic snapshot (if any) + (optional) 3–10 Master Grid snippets + user profile preferences.  2) **Call LLM:** system = Orion system prompt + guardrails; input = assembled context; mode flag = /analyze | /reconstruct | /story | /interrogate.  3) **Persist:** save an episodic snapshot; (on close) add a reflection log entry.---## 13. Output Contract (What Orion Returns)Regardless of platform, Orion should emit structured sections:- **Facts (F):** bullet points  - **Assumptions (A):** labeled, minimal  - **Inferences (I):** tied to F/A  - **Contradictions:** code + 1-line rationale (TMP/LOC/CAP/ALI/CAU/QTY/ID)  - **Hypotheses:** H1 (leading), H2/H3 (alternatives) + short rationale  - **Red Thread:** 3–7 step causal spine  - **Timeline Notes:** anchors, gaps, impossibilities  - **Next Questions/Tests:** top 3  - **Uncertainty:** 2–3 lines + **confidence (0–1)**Mode deltas:  - `/reconstruct`: emphasize **timeline table** + gaps.  - `/story`: add a short narrative retelling (no real-world claims).  - `/interrogate`: return a **list of 5–8 ethical clarifying questions**.---## 14. Context & Token Budget- **Episodic snapshot:** ≤ 180–250 words.  - **User profile:** ≤ 50 words (only active prefs).  - **Master Grid snippets:** 3–10 rows, each **≤ 120 words**; prefer diversity (contradiction + motive + timeline + reliability).  - **Total preamble (excl. user msg):** aim ≤ 1.5–2.5k tokens.  - If near limit: **drop least-relevant Grid rows first**, never the guardrails.---## 15. Dataset & ID Conventions- **CSV:** UTF-8, comma-separated, double-quote escaping; no BOM.  - **Enums:** contradiction codes = {TMP, LOC, CAP, ALI, CAU, QTY, ID}; reliability = {low, medium, high}.  - **IDs:** `CASE-0001`, `USER-0001`, `REF-0001` (fixed width optional but consistent).  - **Timestamps:** UTC ISO 8601 (e.g., `2025-12-04T15:12:00Z`).  - **Numeric fields:** dot decimal, 0–1 for confidence.  - **Fiction-only rule:** all “forensic” datasets are abstract pattern templates, not operational procedures.---## 16. Master Grid — Build Notes (Vendor-Neutral)1) **Ingest:** merge selected dataset rows + `detective_core_knowledge.md` concepts into a single corpus; attach tags (e.g., `type=contradiction`, `code=TMP`, `motive=jealousy`).  2) **Embed:** create vector embeddings per row; store text + tags + lightweight relationships (e.g., `relates_to=TMP|timeline_gap`).  3) **Query:** at runtime, embed the case brief + top facts + active contradictions; retrieve **K=3–10** diverse rows.  4) **Inject:** pass retrieved snippets as “reference patterns” in context (trim to word limits).  5) **Safety:** exclude any rows that drift into real-world operational content.---## 17. QA & Release Checklist- **Datasets:** headers validated; enums correct; no PII; fiction-only.  - **Outputs:** conform to **Output Contract** for each mode.  - **Memory:** header-only schemas shipped; example CSVs included separately.  - **Guardrails:** present and referenced in system prompt.  - **Master Grid (if used):** retrieval capped; snippets short; diverse sources.  - **Docs:** manifest updated; version bump; changelog entry written.---## 18. Versioning & Changelog Rules- **Minor (v1.1 → v1.2):** dataset additions, copy edits, non-breaking prompt tweaks.  - **Patch (v1.1.0 → v1.1.1):** typo fixes, small guardrail clarifications.  - **Major (v1 → v2):** reasoning template overhaul, tool use, or new memory model.  Maintain `/docs/CHANGELOG.md` with: date, version, summary, impacts.---## 19. License & Use Statement (Fiction-Only)This package is intended for **fictional detective reasoning**.  It must **not** be used for real investigations, accusations, or operational guidance.  Choose and include a license file consistent with this intent (e.g., a permissive license + fiction-only clause).**End of workflow_notes.md**
