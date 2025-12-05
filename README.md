# ORION-Adaptive-Hybrid-Detective-Agent
A reasoning-driven detective agent designed for case analysis, contradiction mapping, and adaptive hypothesis generation.

﻿# ORION DETECTIVE AGENT  
[AGENTARIUM_ASSET] 

Name: Orion — Adaptive Hybrid Detective Agent  Version: v1.0  Status: Release  ---# 

Introduction
**Orion** is Agentarium’s first flagship open-source detective agent — a fully structured, memory-enabled, dataset-driven artificial mind designed for **fictional detective reasoning**, narrative case exploration, contradiction detection, hypothesis generation, and timeline reconstruction.Orion is built using the **Agentarium v1 Agent Package Standard**,
meaning:- 
all components are modular  - memory schemas are explicit  - datasets are synthetic & pattern-based  - reasoning is structured and reproducible  - guardrails guarantee fictional, safe operation  - the agent runs on **any LLM or agent builder platform**  This is not “just a prompt.”  This is a **packaged intelligence module**.---

# 🚀 What Orion Can Do### **🔍 1. Structured Detective Analysis (/analyze mode)**  - Extracts facts, assumptions, and inferences  - Identifies contradictions using a custom taxonomy (TMP/LOC/CAP/ALI/CAU/QTY/ID)  - Generates multiple hypotheses  - Ranks them with provisional confidence  - Builds a causal “Red Thread”  - Highlights timeline gaps and impossibilities  ### **📚 2. Story & Narrative Mode (/story)**  - Retells events as a detective narrative  - Optional noir tone  - Uses case archetypes + motive structures  ### **⏱️ 3. Timeline Reconstruction (/reconstruct)**  - Identifies event anchors  - Detects temporal inconsistencies  - Produces a clean timeline table  ### **❓ 4. Ethical Interrogation Mode (/interrogate)**  - Asks structured, non-coercive, clarifying questions  - Uses behavioral & sensory anomaly datasets  - Fully aligned with guardrails  ---# 

What’s Inside This Agent Package Orion ships with the full Agentarium v1 structure:

/orion_detective_agent/ 
/meta/ agent_manifest.json /core/ system_prompt.md reasoning_template.md personality_fingerprint.md /datasets/ (15+ synthetic detective datasets) detective_core_knowledge.md /memory_schemas/ episodic_case_memory_schema.csv episodic_case_memory_schema_examples.csv user_profile_schema.csv user_profile_schema_examples.csv reflection_log_schema.csv reflection_log_schema_examples.csv /guardrails/ guardrails.md 
/docs/ workflow_notes.md 
product_readme.md

Everything is designed to be **readable, extendable, and modular**.---# 🔎 Dataset Ecosystem (Included)Orion comes with a rich synthetic dataset library, including:- **case_archetypes_merged.csv**  - **contradiction_patterns.csv** & **contradictionsV2_patterns.csv**  - **motive_patterns.csv**  - **behavioral_anomalies.csv**  - **perception_witness_reliability_matrix.csv**  - **scenario_timelines_patterns.csv**  - **interrogation_dataset.csv**  - **trace_evidence_matrix.csv**, **evidence_probability_matrix.csv**, etc.  - **forensic pattern datasets** (fictional, abstract logic only)  - **microscene_reconstruction.csv**  - **sensory_anomaly_interr.csv**, **motivational_anomaly_interr.csv**  And more.These datasets **do not** provide real forensic advice.  They function as **pattern sources** to enrich Orion’s reasoning diversity.
---
# Master Grid (Optional Power-Feature)Orion optionally supports a **Master Grid**:> A vectorized knowledge graph built from datasets + core knowledge, enabling richer intra-dataset reasoning and emergent patterns.You can use Pinecone, Chroma, Weaviate, pgvector, or any vector store — Orion is 100% platform-agnostic.The Master Grid lets Orion detect:- subtle contradiction relationships  - deeper motive alignments  - timeline correlations  - witness reliability patterns  - abstract forensic pattern shapes  It is **not required**, but elevates Orion from a good detective agent to a *great* one.---

# 🧬 Memory SystemThree memory schemas are included:### **1. Episodic Case Memory**  Stores compact snapshots of each case: brief, facts summary, contradictions, hypotheses, next tests, confidence.### **2. User Profile Memory**  Stores preferences: mode, detail level, noir flavor, hypothesis depth, language, safety tolerance.### **3. Reflection Log**  Stores meta-reasoning notes after case closures.Each schema is provided in:  - header-only format (for actual use)  - example CSV format (for reference)---

# 🛡️ GuardrailsOrion includes a strong guardrail layer ensuring:- fictional-only reasoning  - no real-world accusations  - no diagnostic claims  - no operational forensics  - no PII storage  - ethical interrogation  - safe redirections  This makes Orion safe for public demos, marketplaces, and deployments.---

# ⚙️ Integration & CompatibilityOrion works with **any LLM**:- OpenAI GPT models  - Anthropic Claude models  - Google Gemini / DeepMind models  - Llama models (local or hosted)  - Deepseek  - Mistral  - Any API-compatible model  Orion also works with **any agent-building framework**:- LangChain  - LlamaIndex  - LangGraph  - n8n  - Flowise  - Custom Python / JS orchestrators  - Relevance AI  - Hugging Face Agents  There are **no dependencies** and no platform lock-in.---

# 🔧 How to Use (Quickstart)1. **Load system prompt + reasoning template + guardrails**  2. Optional: Load memory snapshot for the active `case_id`  3. Optional: Retrieve K=3–10 Master Grid patterns  4. Call the LLM  5. Save a new episodic snapshot  6. On case close, save a reflection log entry  You can implement this in **any language or platform**.---

# 📑 Licensing & Safety NotesThis agent is intended exclusively for **fictional detective reasoning**.  It must not be used for:- real investigations  - real accusations  - legal advice  - operational crime guidance  Forensic datasets inside this package are **abstract logical templates**, not real forensic procedures.Choose a license compatible with this limitation (MIT + Fiction-Only Notice, CC-BY-SA, etc.).---

# ⭐ Why Orion MattersOrion is the **first example** of what Agentarium stands for:- **Downloadable artificial minds**  - Structured reasoning packages  - Modular components  - Memory-driven intelligence  - Synthetic dataset ecosystems  - Safe, ethical design  - Platform freedom  This is not only a detective agent —  it is the **flagship template** for future Agentarium products.---# 🏁 Final NotesIf you extend Orion:- Use the memory schemas  - Add datasets carefully  - Maintain guardrail compatibility  - Update your manifest + changelog  - Consider building your own Master Grid  Welcome to the next era of agent engineering.  Welcome to Agentarium.
**End of product_readme.md**---
