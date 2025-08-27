# Design Plan (First Pass from Nate's Understanding) (v2025-08-26)

> **Constraint**: Conceptual design and pseudocode only. No implementation yet.
> **Core Principle**: "Speed-of-thought" capture with minimal latency between thought and preservation

## 1) Needs (from goals → capabilities)
- **Speed-of-thought capture** → Minimal friction to mint a Bar; optional streaming expansions (10/100/500).  
- **"Mech suit" ergonomics** → Fast input → durable atoms (Bars) → browsable relationships → on-demand drafting
- **Typed relations** → Express *supports/contradicts/refines/is-part-of/see-also* with rationale/evidence.  
- **Search & browse** → Tag & text search; graph neighborhoods; filters by expansion length & quadrant.  
- **Draft-on-demand** → Compile subgraphs into sections/chapters with citations to Bars and relations.  
- **"Tweakable knobs"** → YAML front door for expansion lengths, drafting modes, retrieval policy, relation types.  
- **Evaluation** → Groundedness & citation checks before accepting long-form drafts.
- **Book generation** → AI agents that can "produce a book at any moment's notice" from organized information

## 2) High-level architecture (conceptual)
- **Domain**: Immutable `Bar` (with memetic quality); mutable `Expansion`; typed `Edge` with rationale/evidence; optional `Outline` (or use `is-part-of` subgraphs).  
- **Indices**: BM25/lexical over short text; vector embeddings over expansions; graph store for edges.  
- **Agents** (Pydantic AI): `Capture`, `Expansion`, `Linker`, `Drafter`, `Curator`, `Checker`.  
- **Interface**: Panel + FastAPI (no front-end development burden); global hotkey → "New Bar" modal; graph & board views; cite-aware drafting.  
- **Config**: `author-os.yaml` reloaded on change; Wendell edits without code.
- **Development tooling**: VS Code + Copilot/MCP for building, leveraging built-in agentic capabilities

## 3) Enhanced config (YAML – edited by Wendell, no code changes needed)
```yaml
capture:
  hotkey: "ctrl+alt+b"
  voice_hotkey: "ctrl+alt+v"     # NEW: voice-to-Bar
  expansion_defaults: {w10: true, w100: false, w500: false}
  style_profile: "mythopoetic"
  context_preservation: "dialogue" # preserve conversational context
  auto_suggest_relations: true

drafting:
  mode: "tight"                  # tight | explainer | narrative
  max_refs: 7
  cite_style: "inline"           # inline | footnote
  book_generation_mode: "guided" # guided | auto
  preserve_contradictions: true  # surface creative tension

retrieval:
  prefer: ["tags","bm25_w10","vector_w100","graph_neighbors"]
  graph_hops: 1
  neighborhood_types: ["supports","refines","contradicts"]
  semantic_threshold: 0.7        # for auto-linking

relations:
  allowed_types: ["supports","contradicts","refines","is_part_of","see_also"]
  require_rationale: true
  auto_detect_contradictions: true
  strength_weighting: false      # optional: confidence scores

expansions:
  w10:   {target_words: 10,  style_profile: "aphorism"}
  w100:  {target_words: 100, style_profile: "exegesis"}  
  w500:  {target_words: 500, style_profile: "essaylet"}
  story: {target_words: 300, style_profile: "narrative"}
  exercise: {target_words: 150, style_profile: "practical"}

ui:
  panels: ["Capture","Browse","Graph","Draft","Config"]
  default_panel: "Capture"
  mobile_minimal: true          # minimal mobile interface
  
integral_theory:
  enable_quadrants: true
  auto_classify: false          # manual quadrant assignment
  development_lines: ["cognitive", "moral", "interpersonal"]
```

## 4) Data model (pseudocode)
```text
model Bar:
  bar_id: ULID
  slug: str
  text: str                    # immutable canonical phrase
  tags: list[str]
  quadrant: enum?              # optional integral quadrant
  creator: str
## 4) Enhanced data model (pseudocode with conversation insights)
```text
model Bar:
  bar_id: ULID
  slug: str
  text: str                    # immutable canonical phrase (memetic quality)
  tags: list[str]
  quadrant: enum?              # optional integral quadrant
  creator: str
  created_at: datetime
  source_context: str|URI      # conversational context, book highlight, etc.
  dialogue_context: markdown? # preserve "that's a Bar" moment
  memetic_quality: float?      # how "catchy" or memorable

model Expansion:
  expansion_id: ULID
  bar_id: Bar.ref
  kind: enum(w10,w100,w500,story,exercise,example,analogy,dialogue)
  body: markdown
  style_profile: str           # mythopoetic, clinical, narrative, etc.
  created_at: datetime
  history: list[Change]
  usage_count: int             # track which expansions get used in drafts

model Edge:
  src: Bar.ref
  type: enum(supports,contradicts,refines,is_part_of,see_also,emerges_from)
  dst: Bar.ref
  rationale: markdown
  evidence: list[ExcerptRef]
  strength: float?             # optional confidence/strength
  created_at: datetime

model Draft:
  draft_id: ULID
  title: str
  mode: enum(tight,explainer,narrative)
  content: markdown
  citations: list[BarRef|EdgeRef]
  groundedness_score: float
  created_at: datetime
```

## 5) Enhanced retrieval policy (speed-of-thought optimized)
1. **Intent detection**: If user intent looks aphoristic → search `Bar.text` + `w10`.  
2. **Semantic blending**: Else blend vector over `w100/w500` + tag matching.  
3. **Graph traversal**: Pull 1–2 graph hops of neighbors by configured edge types.  
4. **Contradiction awareness**: Include contradictory Bars to surface creative tension.
5. **Recency weighting**: Rank by tag overlap + recency of expansions + usage patterns.  
6. **Explicit provenance**: Provide **explicit citations** (bar_id and edge rationale ids).
7. **Context awareness**: Weight results by current conversation/session context.

## 6) Enhanced drafting modes (supporting the "mech suit" metaphor)
- **tight**: prefer `w10`, minimal interpolation; cite every claim; aphoristic style.  
- **explainer**: blend `w100`, include `supports/refines` neighbors; didactic clarity.  
- **narrative**: pull analogies/stories; allow longer connective tissue; mythopoetic voice.
- **dialectical**: explicitly surface contradictions and explore tensions.
- **integral**: organize by quadrants and development lines.

## 7) Core flows (pseudocode emphasizing speed and ergonomics)

### Enhanced capture flow (speed-of-thought priority)
```text
on_new_bar(text, tags?, quadrant?, source?, voice_note?):
  # Immediate persistence - no blocking operations
  id = ULID()
  bar = Bar(id, text, tags, quadrant, source, voice_note)
  save_bar_immediately(bar)  # < 100ms target
  
  # Background processing
  if cfg.capture.expansion_defaults.w10: 
    queue_expansion_async(id, "w10", cfg.expansions.w10.style_profile)
  if cfg.capture.auto_suggest_relations:
    queue_relation_suggestions_async(id)
  
  # Return immediately
  return {"bar_id": id, "status": "captured", "processing": "background"}
```

### Voice-to-Bar flow (new from conversation)
```text
on_voice_capture(audio_blob):
  # Stream transcription for immediate feedback
  text = stream_transcribe(audio_blob)  # show real-time
  
  # Extract potential Bars from speech
  bar_candidates = extract_bar_phrases(text)
  
  # Present for confirmation with one-click minting
  return {"transcription": text, "candidates": bar_candidates}
```

### Smart expansion generation
```text
generate_expansion(bar_id, kind, style_profile):
  bar = load_bar(bar_id)
  context = get_related_bars(bar_id, hops=1)  # neighborhood context
  
  prompt = build_expansion_prompt(
    bar=bar, 
    kind=kind, 
    style_profile=style_profile,
    context=context,
    integral_quadrant=bar.quadrant
  )
  
  # Stream generation for immediate feedback
  body = stream_LLM_expansion(prompt)
  expansion = save_expansion(bar.id, kind, body, style_profile)
  
  # Background: suggest relations based on new expansion
  if kind in ["w100", "w500"]:
    queue_relation_discovery(expansion)
  
  return expansion
```

### Intelligent relation discovery
```text
discover_relations(bar_id):
  bar = load_bar(bar_id)
  candidates = semantic_search(bar.text + bar.expansions, k=20)
  
  # AI-powered relation classification
  for candidate in candidates:
    relation_type, confidence, rationale = classify_relation(bar, candidate)
    if confidence > cfg.relations.auto_suggest_threshold:
      propose_edge(bar_id, relation_type, candidate.id, rationale, confidence)
```

### Book generation flow (the ultimate goal)
```text
generate_book(outline_bars, mode="guided"):
  if mode == "guided":
    structure = get_user_structure_input(outline_bars)
  else:
    structure = auto_generate_structure(outline_bars)
  
  book_sections = []
  for section in structure:
    relevant_context = retrieve_for_section(section.bars, section.theme)
    section_draft = draft_section(relevant_context, section.mode)
    section_draft = validate_groundedness(section_draft)
    book_sections.append(section_draft)
  
  return compile_book(book_sections, citations=True, style=cfg.drafting.style)
```

### Draft compile (subgraph → markdown)
```text
compile_outline(root_bar_id, mode):
  nodes = traverse_is_part_of(root_bar_id, cfg.retrieval.graph_hops)
  context = retrieve(nodes, mode)
  draft = render_markdown(context, mode, cite_style=cfg.drafting.cite_style)
  assert grounded(draft)     # CheckerAgent gate
  return draft
```

## 8) Evaluation (lightweight)
- Use groundedness/answer relevance/context precision on samples before accepting long-form drafts.  
- Human-in-the-loop approvals for contradiction handling and tone.

## 9) Risks & mitigations
- **Over-structuring early** → start with Bars/Edges/Expansions only; add complexity later.  
- **Latency** → persist Bars synchronously; push expansions/links async.  
- **Drift** → keep Bars immutable; version expansions/edges.
