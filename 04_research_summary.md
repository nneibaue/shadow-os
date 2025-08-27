# Research Summary for Author OS (distilled to applicable techniques) (v2025-08-26)

A practical synthesis of agentic/RAG techniques suited to this project, informed by the conversation about "speed-of-thought" cognitive amplification.

## Retrieval patterns perfectly suited for the "mech suit" metaphor
- **GraphRAG**: Build a lightweight knowledge graph over Bars and typed edges; use graph neighborhoods to seed context, then summarize/query. Perfect for connecting *supports/contradicts/refines* during drafting while preserving the "grammar of ontology."
- **Self-RAG**: Let the drafter decide when/what to retrieve and self-critique — crucial for long-form sections to reduce spurious claims while maintaining intellectual honesty.  
- **CRAG (Corrective RAG)**: Add retrieval-quality check; if weak, switch strategies (favor tags/graph, or widen search) before drafting. Critical for maintaining citation integrity.  
- **Section-aware retrieval**: Treat expansions as coherent sections (10/100/500); avoid fragmenting a Bar's meaning across arbitrary chunks. Preserves the memetic quality of each Bar.
- **Multi-modal RAG**: Support voice-to-text capture, document highlighting, and conversational context extraction.

## Orchestration & memory (Pydantic AI emphasis)
- **Typed agents (Pydantic AI)** keep IO crisp with schemas for `Bar`, `Edge`, `Expansion`, `Draft` — exactly what's needed for the config-driven approach.  
- **Simple orchestration first**: Start with linear agent chains; add sophisticated checkpoints/handoffs later if needed.  
- **Externalized memory architecture**: Bars as immutable atoms; expansions/edges provide editable "long-term memory" — perfect for the evolving knowledge base while preserving core insights.
- **Streaming interfaces**: All generation (expansions, drafts) should stream for immediate feedback, supporting "speed-of-thought" interaction.

## Advanced evaluation (intellectual honesty focus)
- **Groundedness + answer relevance + context precision** on spot-checks for drafts. Gate long-form output on passing thresholds.
- **Citation integrity**: Every claim must trace back to a specific Bar or acknowledged gap.
- **Contradiction detection**: Surface creative tensions between Bars rather than hiding them.
- **Memetic quality assessment**: Evaluate whether generated content preserves the "memetic loot" quality of source Bars.

## Enhanced minimal viable stack
- **Multi-index architecture**: BM25 over `Bar.text`; vectors over `w100/w500`; graph store for typed edges; fuzzy matching for voice transcription.  
- **Agent pipeline**: Capture → Expansion → Linker → Drafter → Checker → BookGenerator.  
- **Interface priorities**: Panel + FastAPI with global hotkeys, "New Bar" modal, graph visualization, and real-time collaboration features.  
- **Configuration-first**: YAML knobs for every aspect — retrieval policy, drafting modes, relation types, expansion defaults, style profiles.
- **Development acceleration**: VS Code + Copilot/MCP integration for rapid prototyping and system prompts.

## Conversation-specific insights
- **Latency optimization**: Sub-100ms capture-to-storage is critical; everything else can be async.
- **Context preservation**: Maintain conversational context around "that's a Bar" moments.
- **Ergonomic design**: If it doesn't support speed-of-thought capture, it's architecturally wrong.
- **Integral theory integration**: Optional quadrant classification and development line tracking from day one.
- **Collaborative knowledge building**: Design for multiple practitioners sharing the same ontological framework.

## Technical constraints from conversation
- **Non-negotiable**: Pydantic AI for agent framework
- **Interface mandate**: Panel + FastAPI (minimal front-end development)
- **Configuration approach**: Wendell must be able to "tweak the knobs" without touching code
- **Development strategy**: Leverage VS Code's built-in agentic tooling rather than building everything from scratch