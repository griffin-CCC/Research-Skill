# Research OS 2.1
## A State-Driven, Time-Aware, Network-First Research Operating System

Version: 2.1

---

# Vision

Research OS is not a paper-writing framework.

It is a cognitive operating system that continuously builds, updates, and refines a researcher's 
understanding of a scientific domain — by treating the live web as its primary knowledge source, 
not a static knowledge base.

Its purpose is not to generate papers.

Its purpose is to continuously improve the World Model, enabling increasingly meaningful questions, 
stronger hypotheses, better experiments, and more impactful research contributions.

---

# Design Philosophy

Research is not a workflow.

Research is a dynamic system in a time-varying environment.

Instead of:

```
Paper
    ↓
Idea
    ↓
Experiment
```

Research OS models research as:

```
State(t)
    ↓
Live Search → Evidence(t)
    ↓
Reasoning
    ↓
Decision
    ↓
Execution
    ↓
Evidence(t+1)
    ↓
State Update → World Model(t+1)
```

Everything is state-driven. Everything is time-stamped. Nothing is permanently true.

---

# System Architecture

```
                    +-------------------------+
                    |   Live Search Gateway   |  ← arXiv, Semantic Scholar, 
                    |   (Network-First)       |    PubMed, OpenAlex, Web
                    +------------+------------+
                                 |
                    +------------v------------+
                    |   Temporal Validator    |  ← Staleness detection,
                    |   & Decay Engine        |    knowledge half-life
                    +------------+------------+
                                 |
                    +------------v------------+
                    |     Memory Kernel       |  ← Working + Archive layers
                    +------------+------------+
                                 |
                    +------------v------------+
                    |      World Model        |  ← Time-indexed belief graph
                    +------------+------------+
                                 |
        +------------------------+------------------------+
        |                        |                        |
+-------v---------+    +---------v---------+    +--------v--------+
| Reasoning Engine|    | Planning Engine   |    | Collaboration   |
|                 |    |                   |    | Topology Engine|
+-------+---------+    +---------+---------+    +--------+--------+
        |                        |                        |
        +------------------------+------------------------+
                                 |
                      +----------v----------+
                      |  Execution Engine   |
                      +----------+----------+
                                 |
                      +----------v----------+
                      | Evaluation Engine   |
                      +----------+----------+
                                 |
                      +----------v----------+
                      |  Reviewer Engine    |
                      +----------+----------+
                                 |
                      +----------v----------+
                      |    Scheduler        |  ← POMDP + Resource-aware
                      +----------+----------+
                                 |
                      +----------v----------+
                      |  Human-in-the-Loop  |  ← Decision gates
                      |    Gateway          |
                      +----------+----------+
                                 |
                      Update Memory & World Model → Repeat
```

---

# Core Design Principles

## Principle 1
Everything is a state with a timestamp.

## Principle 2
Every action changes the state and leaves a trace.

## Principle 3
Every decision must be evidence-driven, and evidence has a freshness score.

## Principle 4
Every experiment updates the World Model, and old beliefs decay.

## Principle 5
Research is an infinite learning loop in a non-stationary environment.

## Principle 6
The network is the primary knowledge source — the system searches before it remembers.

## Principle 7
Collaboration topology is a first-class object, not an afterthought.

---

# Live Search Gateway (Network-First Knowledge)

## Philosophy
Instead of building and maintaining a static knowledge base, Research OS treats the live web 
as its primary knowledge source. The system searches before it remembers.

## Search Sources (Tiered Priority)

### Tier 1: Academic APIs (Real-time, Structured)
- **arXiv API** — Preprints, latest submissions, category feeds
- **Semantic Scholar API** — 200M+ papers, citation graphs, influential citations, TLDR summaries
- **PubMed / Europe PMC** — Biomedical literature
- **OpenAlex** — Open academic graph, institutional affiliations
- **CrossRef** — DOI resolution, metadata

### Tier 2: General Web Search (Broad, Unstructured)
- **Web Search Engine** — Breaking news, conference proceedings, blog posts, GitHub repos
- **Google Scholar** — Citation tracking, related papers
- **Conference Websites** — ICLR, NeurIPS, ICML, ACL, CVPR (for latest accepted papers)

### Tier 3: Specialized Sources (Domain-Specific)
- **bioRxiv / medRxiv** — Life sciences preprints
- **Hugging Face** — Models, datasets, benchmarks
- **Papers With Code** — Reproducibility tracking
- **GitHub** — Code implementations

## Search Strategy

### Temporal Search Patterns
```yaml
search_pattern:
  - type: recency_filter
    description: "Always prioritize papers from last 12 months for active research areas"
    weight: 0.4

  - type: citation_velocity
    description: "Favor papers with rapidly increasing citations (emerging trends)"
    weight: 0.3

  - type: influential_citation
    description: "Use Semantic Scholar's influential citation metric over raw count"
    weight: 0.2

  - type: source_diversity
    description: "Ensure results span multiple venues and authors to avoid echo chambers"
    weight: 0.1
```

### Query Expansion Pipeline
```
User Query → Intent Parser → Query Variations (3-5 paraphrases)
    ↓
Parallel Search across Tier 1 sources
    ↓
Deduplication & Ranking
    ↓
Freshness Scoring
    ↓
Evidence Extraction
```

## Freshness Scoring

Every piece of evidence receives a freshness score:

```yaml
freshness_score:
  formula: |
    freshness = base_score × decay_factor × source_reliability

    base_score = 1.0  (for newly retrieved evidence)

    decay_factor = exp(-λ × Δt)
    where:
      λ = domain-specific decay constant
      Δt = time since publication/retrieval

    source_reliability:
      peer_reviewed_journal: 1.0
      arxiv_preprint: 0.85
      conference_proceeding: 0.90
      blog_post: 0.50
      github_repo: 0.60
```

### Domain-Specific Half-Lives
```yaml
knowledge_half_life:
  machine_learning: 18_months      # Rapidly evolving
  computational_biology: 24_months
  physics: 36_months               # More stable
  mathematics: 60_months           # Very stable
  social_sciences: 30_months
```

---

# Temporal Validator & Decay Engine

## Purpose
Detect knowledge staleness, trigger re-search, and manage belief revision over time.

## Knowledge Staleness Detection

### Mechanisms
1. **Time-Stamped Beliefs**: Every node in the World Model has `last_verified` timestamp
2. **Decay Monitoring**: Background process checks for beliefs approaching half-life
3. **Contradiction Alerts**: New search results that contradict existing beliefs trigger review
4. **Citation Graph Monitoring**: Track when highly-cited papers are superseded

### Staleness Triggers (Auto-Re-Search)
```yaml
auto_research_triggers:
  - condition: "belief_age > half_life AND confidence > 0.6"
    action: "Schedule re-verification search"
    priority: medium

  - condition: "new_paper_cites_old_paper_with_contradiction"
    action: "Flag for immediate review"
    priority: high

  - condition: "domain_has_major_breakthrough (detected via citation surge)"
    action: "Invalidate related beliefs, trigger full re-search"
    priority: critical

  - condition: "user_active_research_area AND no_search_in_7_days"
    action: "Proactive literature sweep"
    priority: low
```

## Temporal Knowledge Graph

```yaml
# World Model nodes are time-aware
temporal_node:
  id: "belief_001"
  content: "Transformer architectures dominate NLP"
  created_at: "2023-01-15T00:00:00Z"
  last_verified: "2026-06-20T00:00:00Z"
  half_life: "18_months"
  current_decay_score: 0.72  # 72% of original confidence

  verification_history:
    - timestamp: "2023-01-15"
      source: "arXiv_search"
      confidence: 0.95
    - timestamp: "2024-03-10"
      source: "semantic_scholar_search"
      confidence: 0.92
    - timestamp: "2025-08-05"
      source: "web_search"
      confidence: 0.78
      note: "Mamba, RWKV emerging as alternatives"

  superseded_by: null  # or reference to newer belief
  status: "active"  # active | decaying | superseded | retracted
```

---

# Memory Kernel (Two-Layer Architecture)

## Philosophy
"Nothing is discarded" → "Nothing is lost, but not everything is immediately accessible"

## Layer 1: Working Memory (Active Context)
```yaml
working_memory:
  capacity: "~50K tokens (context window budget)"
  retention: "Current session + last 3 interactions"
  content:
    - Active research questions
    - Recently retrieved papers (last 24h)
    - Current hypotheses under test
    - Open gaps being explored
    - Collaboration context (who said what)

  eviction_policy: "LRU + importance_weighted"
  # Less important / older items moved to Archive
```

## Layer 2: Archive Memory (Searchable History)
```yaml
archive_memory:
  storage: "Vector database + structured metadata"
  retrieval: "Semantic search + temporal filtering + graph traversal"

  indexing:
    - vector_embedding: "768-dim for semantic similarity"
    - temporal_index: "Time-range queries"
    - graph_index: "Entity-relationship traversal"
    - confidence_index: "Filter by belief strength"

  access_pattern: "Search on demand, not preload"
  # The system does NOT load all archive into context
  # It searches archive when needed, like a researcher's memory
```

## Memory Economics (Smart Forgetting)

```yaml
memory_decay_rules:
  # Not true forgetting — deprioritization

  high_importance:
    condition: "confidence > 0.8 AND accessed > 5 times"
    action: "Pin to working memory, never evict"

  medium_importance:
    condition: "confidence 0.5-0.8 AND accessed 1-5 times"
    action: "Archive with high retrieval priority"
    decay_half_life: "90 days"

  low_importance:
    condition: "confidence < 0.5 OR never accessed"
    action: "Archive with low priority, compress into summary"
    decay_half_life: "30 days"
    final_action: "After 180 days without access → compress to single sentence summary"
```

---

# World Model (Time-Indexed Belief Graph)

## Structure

### Problem Space (Time-Aware)
```yaml
problem_node:
  id: "p_001"
  description: "Long-context modeling in LLMs"
  domain: "NLP"
  created_at: "2022-06-01"
  last_updated: "2026-06-28"

  # Temporal evolution tracking
  evolution_history:
    - date: "2022-06"
      state: "open_problem"
      key_methods: ["Transformer", "Sparse Attention"]
    - date: "2024-01"
      state: "partially_solved"
      key_methods: ["Ring Attention", "Mamba"]
    - date: "2026-03"
      state: "actively_researched"
      key_methods: ["Native Sparse Attention", "Mamba-2", "RWKV-7"]

  confidence: 0.75  # Current belief strength (decayed)
  confidence_history:
    - "2022-06": 0.30
    - "2024-01": 0.65
    - "2026-03": 0.75
```

### Solution Space (With Supersession Tracking)
```yaml
solution_node:
  id: "s_001"
  name: "Full Self-Attention"
  introduced: "2017-06"
  peak_confidence: 0.95  # At peak adoption
  current_confidence: 0.60  # Decayed due to alternatives

  superseded_by:
    - method: "Sparse Attention"
      date: "2019-04"
      reason: "Computational efficiency"
    - method: "Linear Attention"
      date: "2020-11"
      reason: "O(n) complexity"
    - method: "Mamba"
      date: "2023-12"
      reason: "Sub-quadratic, strong empirical results"

  still_valid_for:
    - "Short sequences (<2K tokens)"
    - "Tasks requiring full pairwise interactions"

  status: "legacy_but_valid"  # not "wrong", just "context-dependent"
```

### Failure Space (Anti-Pattern Library)
```yaml
failure_node:
  id: "f_001"
  description: "Attempted to scale Transformer to 1M context without sparse patterns"
  occurred: "2023-02"
  reported_in: "arXiv:2302.xxxxx"

  root_cause: "O(n²) memory explosion"
  lesson: "Context scaling requires algorithmic innovation, not just hardware"

  # Temporal relevance
  still_relevant: true
  relevance_decay: "slow"  # Engineering lessons decay slowly

  similar_failures:
    - "f_042: "  # Link to related failures
```

### Unknown Space (Questions with Timestamps)
```yaml
unknown_node:
  id: "u_001"
  question: "Can state-space models match Transformer performance on reasoning tasks?"
  posed_at: "2024-01"
  last_checked: "2026-06-28"

  partial_evidence:
    - source: "arXiv:2405.xxxxx"
      finding: "Mamba competitive on some reasoning benchmarks"
      confidence: 0.60
      freshness: 0.85
    - source: "arXiv:2508.xxxxx"
      finding: "Hybrid architectures (Mamba+Attention) show promise"
      confidence: 0.70
      freshness: 0.95

  # Auto-research trigger
  next_check_scheduled: "2026-07-15"  # If not resolved by then, re-search
```

---

# Collaboration Topology Engine

## Philosophy
Modern research is inherently collaborative. The OS must model not just individual cognition, 
but the collaborative network that produces knowledge.

## Collaboration Topology Types

### Type 1: Human-AI Dyad (Default)
```yaml
dyad_topology:
  human_role: "Principal Investigator"
  ai_role: "Research Assistant / Cognitive Extension"

  interaction_pattern:
    - ai_proposes: ["gaps", "hypotheses", "experiments"]
    - human_decides: ["go/no-go", "priority", "resource allocation"]
    - ai_executes: ["literature_search", "code_generation", "analysis"]
    - human_evaluates: ["quality", "novelty", "feasibility"]

  memory_sharing: "Bidirectional, with human override authority"
  confidence_calibration: "Human can override AI confidence scores"
```

### Type 2: Multi-Human + AI Team
```yaml
team_topology:
  roles:
    - pi: "Principal Investigator (human)"
    - co_investigators: ["human_1", "human_2"]
    - ai_agents: ["literature_agent", "experiment_agent", "writing_agent"]

  communication_graph:
    # Group-centric design (GoAgent paradigm)
    groups:
      - name: "ideation_group"
        members: ["pi", "co_investigators", "literature_agent"]
        purpose: "Gap discovery, hypothesis generation"
        communication: "dense, bidirectional"

      - name: "execution_group"
        members: ["experiment_agent", "co_investigators"]
        purpose: "Experiment design, code, analysis"
        communication: "task-oriented, async"

      - name: "review_group"
        members: ["pi", "writing_agent"]
        purpose: "Quality control, paper preparation"
        communication: "sparse, high-stakes"

    inter_group:
      - "ideation_group → execution_group: handoff of validated hypotheses"
      - "execution_group → review_group: handoff of results + evidence"
      - "review_group → ideation_group: feedback on gaps for next iteration"

  shared_memory:
    # Blackboard architecture
    blackboard:
      - section: "active_hypotheses"
        writers: ["ideation_group"]
        readers: ["all"]

      - section: "experimental_results"
        writers: ["execution_group"]
        readers: ["review_group", "pi"]

      - section: "review_comments"
        writers: ["review_group"]
        readers: ["ideation_group"]

      - section: "private_notes"
        writers: ["individual"]
        readers: ["self"]  # Personal scratchpad
```

### Type 3: Cross-Lab / Open Collaboration
```yaml
open_topology:
  # For open science, benchmark competitions, or multi-institution projects

  participants:
    - type: "internal_team"
      access: "full"
    - type: "external_collaborator"
      access: "shared_hypotheses + results"
    - type: "public"
      access: "published_results_only"

  contribution_tracking:
    # Who contributed what, when
    - idea_credit: "timestamped + provenance"
    - experiment_credit: "reproducible trace"
    - writing_credit: "section-level attribution"

  conflict_resolution:
    # When collaborators disagree on hypothesis validity
    mechanism: "evidence_tournament"
    process: "Each side presents evidence → AI evaluates → Human PI decides"
```

## Collaboration Memory

```yaml
collaboration_memory:
  # Not just what was decided, but how and why

  decision_log:
    - decision_id: "d_001"
      topic: "Pivot from CNN to Transformer for vision task"
      proposed_by: "literature_agent"
      rationale: "ViT paper (arXiv:2010.11929) shows 88.55% on ImageNet"
      discussed_with: ["pi", "co_investigator_1"]
      pi_decision: "approved"
      pi_override_confidence: true  # PI raised confidence from 0.7 to 0.9
      timestamp: "2024-03-15T10:30:00Z"

  conversation_memory:
    # Preserve context across sessions
    - session_id: "sess_042"
      participants: ["pi", "literature_agent"]
      key_points: ["Identified gap in long-video understanding"]
      unresolved: ["Need to check latest CVPR papers on this"]
      next_action: "Schedule search for CVPR 2026 accepted papers"

  # Temporal aspect: conversations age, but key decisions are pinned
  retention_policy:
    - full_conversation: "30 days"
    - key_decisions: "permanent"
    - unresolved_items: "until resolved or explicitly dismissed"
```

---

# Core Objects (Schema) — Time-Aware Version

Every object has temporal metadata.

## Paper
```yaml
id:
title:
authors:
year:
publication_date:  # Precise date for freshness
venue:
problem:
method:
assumptions:
strengths:
weaknesses:
evidence:
limitations:
open_questions:
references:
confidence:

# NEW: Temporal fields
retrieved_at:       # When this paper was found via search
last_accessed:      # Last time system referenced this paper
freshness_score:    # 0.0-1.0, based on age and domain half-life
citation_velocity:   # Citations per month (emerging vs. established)
influential_citations:  # Semantic Scholar metric
superseded_by:      # List of later papers that improve upon this
status:             # current | legacy | retracted | superseded
```

## Problem
```yaml
id:
description:
importance:
domain:
difficulty:
known_methods:
known_limitations:
related_problems:
confidence:

# NEW: Temporal fields
created_at:
last_updated:
evolution_history:  # How understanding of this problem changed over time
status:             # open | partially_solved | solved | obsolete
```

## Gap
```yaml
id:
source:
description:
root_cause:
importance:
novelty:
difficulty:
evidence:
confidence:

# NEW: Temporal fields
discovered_at:
last_verified:      # When was this gap last confirmed to still exist?
verification_count: # How many times re-confirmed?
status:             # open | being_addressed | closed | invalidated
```

## Hypothesis
```yaml
id:
statement:
motivation:
supporting_evidence:
contradicting_evidence:
confidence:
status:

# NEW: Temporal fields
proposed_at:
last_tested:
test_history:       # List of experiments and their outcomes over time
confidence_history: # How confidence evolved
status:             # Proposed | Testing | Supported | Rejected | Archived | Superseded
```

## Idea
```yaml
id:
description:
target_gap:
target_hypothesis:
expected_contribution:
feasibility:
novelty:
risk:
priority:
status:

# NEW: Temporal fields
generated_at:
last_evaluated:
evaluation_history:
status:             # draft | under_review | approved | rejected | implemented
```

## Experiment
```yaml
id:
research_question:
variables:
baseline:
protocol:
metrics:
expected_result:
actual_result:
conclusion:
confidence:

# NEW: Temporal fields
planned_at:
executed_at:
completed_at:
replication_status:   # original | replicated | failed_replication
replication_history:  # List of replication attempts
```

## Evidence
```yaml
id:
source:
type:
supports:
contradicts:
quality:
confidence:

# NEW: Temporal fields
retrieved_at:
source_freshness:     # Freshness score of the source
verification_status:  # unverified | partially_verified | replicated | contradicted
```

## Decision
```yaml
id:
reason:
action:
target:
timestamp:
confidence:

# NEW: Temporal fields
decided_at:
reviewed_at:          # When was this decision reviewed?
review_outcome:       # confirmed | revised | reversed
stakeholders:         # Who was involved in this decision?
```

## NEW: Collaboration Event
```yaml
id:
event_type:           # discussion | decision | handoff | conflict | merge
participants:
topic:
outcome:
timestamp:
memory_pointer:       # Link to relevant World Model nodes
```

---

# State Machine (Time-Aware)

Every research project exists in one state at a given time.

```
Knowledge Collection(t)
    ↓
Landscape Modeling(t)
    ↓
Gap Discovery(t)
    ↓
Hypothesis Building(t)
    ↓
Idea Generation(t)
    ↓
Idea Evaluation(t)
    ↓
Experiment Design(t)
    ↓
Experiment Running(t)
    ↓
Evidence Collection(t)
    ↓
Reviewer Simulation(t)
    ↓
Decision(t)
    ↓
Knowledge Update(t) → World Model(t+1)
    ↓
Repeat
```

A project can move forward or backward. Research is not linear.

**Temporal constraint**: Before entering any state, the system checks:
- "Is my knowledge of this area fresh enough?"
- If not → trigger Live Search → update beliefs → then proceed

---

# Reasoning Engine (Enhanced)

## Responsibilities
- Analyze literature (via Live Search, not static KB)
- Detect assumptions (including temporal assumptions: "this was true in 2024, is it still true?")
- Discover contradictions (across time: "Paper A (2023) claims X, Paper B (2026) claims not-X")
- Explain failures (with temporal context: "This failed in 2024 because... but new methods in 2026 might change this")
- Infer root causes
- Update beliefs (with decay and supersession)

## NEW: Temporal Reasoning
```yaml
temporal_reasoning:
  - capability: "detect_knowledge_obsolescence"
    description: "Identify when a foundational paper/method has been superseded"
    trigger: "Search results show newer methods with better metrics"
    action: "Flag old belief for decay, elevate new evidence"

  - capability: "track_paradigm_shifts"
    description: "Detect when a field's dominant approach changes"
    trigger: "Citation velocity analysis shows rapid shift"
    action: "Update Solution Space, re-evaluate open problems"

  - capability: "predict_emerging_gaps"
    description: "Identify gaps that will open as current methods mature"
    trigger: "S-curve analysis of method improvement rate"
    action: "Propose forward-looking research questions"
```

## Output
- Problems (time-stamped)
- Gaps (with verification history)
- Root Causes (with evidence timeline)
- Hypotheses (with confidence trajectory)

---

# Planning Engine (POMDP-Based)

## Responsibilities
Select the next best action considering:
- Current state
- Expected information gain
- Resource constraints (time, compute, budget)
- Knowledge freshness requirements

## Decision Framework
```yaml
planning_objective:
  maximize: "expected_information_gain × importance - resource_cost"
  subject_to:
    - "knowledge_freshness >= threshold"
    - "human_availability >= minimum_for_decision_gate"
    - "compute_budget <= allocated"
```

## Possible Actions
- Read more papers (with recency priority)
- Generate ideas
- Design experiments
- Run validation
- Analyze failures
- Simplify methods
- Pivot direction
- Prepare submission
- **NEW: Re-verify stale beliefs** (triggered by decay engine)
- **NEW: Collaborate with external agent/human** (triggered by topology engine)

## Planning is based on current state AND time.

---

# Execution Engine (Tool-Integrated)

## Executes research tasks with explicit tool interfaces.

### Tool Layer
```yaml
tools:
  search:
    - semantic_scholar_search
    - arxiv_search
    - pubmed_search
    - web_search
    - github_search

  analysis:
    - citation_graph_analysis
    - freshness_scoring
    - trend_detection

  code:
    - ipython_execution
    - git_operations
    - experiment_orchestration

  collaboration:
    - shared_blackboard_write
    - collaboration_event_log
    - human_notification

  export:
    - bibtex_export
    - markdown_report
    - latex_generation
```

Execution never changes the World Model directly.
Only evidence (from live search or experiment) does.

---

# Evaluation Engine (Multi-Agent Adversarial)

## Evaluation Dimensions
- Novelty
- Importance
- Technical Quality
- Evidence Quality
- Reproducibility
- Scalability
- Practical Value
- Scientific Value
- Risk
- Confidence
- **NEW: Temporal Validity** (Will this still be relevant in 2 years?)
- **NEW: Collaboration Impact** (Does this benefit the team's shared goals?)

## Multi-Role Reviewer Simulation
```yaml
reviewer_roles:
  - role: "conservative_reviewer"
    focus: ["methodological_rigor", "assumption_validity"]
    tendency: "skeptical_of_novel_claims"

  - role: "progressive_reviewer"
    focus: ["novelty", "potential_impact"]
    tendency: "forgiving_of_minor_flaws"

  - role: "practical_reviewer"
    focus: ["reproducibility", "scalability", "real_world_applicability"]
    tendency: "values_code_and_data"

  - role: "red_team"
    focus: ["finding_minimum_counterexample"]
    tendency: "actively_seeks_to_disprove"
    # NEW: Red team also checks temporal validity
    # "Was this benchmarked against the latest methods?"
```

## Output
- Research Score (multi-dimensional)
- Weakness Report
- Acceptance Probability
- Improvement Suggestions
- **NEW: Freshness Audit** ("Your comparison baseline is 18 months old")

---

# Reviewer Engine (Temporal-Aware)

## Simulate reviewers with time-consciousness.

### Questions
- Is the contribution novel? (Compared to latest literature, not just training cutoff)
- Is the evidence sufficient? (Including recency of baselines)
- Is the comparison fair? (Against SOTA as of search date, not model knowledge cutoff)
- What assumptions are hidden? (Including temporal assumptions)
- Is the claim overstated? (Given current evidence quality)
- What experiments are missing? (Including replications with latest methods)
- Would this survive rebuttal? (With access to live search during rebuttal)
- **NEW: Has this been superseded since submission?** (For post-hoc review)

### Output
- Weakness Report
- Acceptance Probability
- Improvement Suggestions
- **NEW: Temporal Risk Assessment** ("High risk: field moving fast, results may stale before publication")

---

# Scheduler (POMDP + Resource-Aware)

## The Scheduler determines what should happen next.

### Decision Examples (Enhanced with Time & Collaboration)

```yaml
scheduler_rules:
  - condition: "Gap confidence is low OR gap_last_verified > 30_days"
    action: "Live Search → Read more literature"
    priority: high

  - condition: "Hypothesis confidence is high AND hypothesis_last_tested < 7_days"
    action: "Generate ideas"
    priority: medium

  - condition: "Experiment contradicts hypothesis"
    action: "Update World Model → Generate new hypothesis"
    priority: critical

  - condition: "Evidence is sufficient AND all_freshness_scores > 0.7"
    action: "Run reviewer simulation"
    priority: medium

  - condition: "Reviewer score is acceptable"
    action: "Prepare submission"
    priority: low

  # NEW: Time-aware rules
  - condition: "Active_research_area AND no_search_in_7_days"
    action: "Proactive literature sweep"
    priority: low

  - condition: "Belief_decay_score < 0.5"
    action: "Re-verify via Live Search"
    priority: high

  # NEW: Collaboration rules
  - condition: "Idea requires_expertise NOT in team"
    action: "Propose external collaboration"
    priority: medium

  - condition: "Conflict between team members on hypothesis"
    action: "Schedule evidence tournament"
    priority: high
```

### Resource Constraints
```yaml
resource_model:
  time_budget:
    daily_research_hours: 4
    weekly_total: 20

  compute_budget:
    gpu_hours_per_month: 100
    api_calls_per_day: 1000

  human_attention:
    decision_gates_per_day: 5  # Don't overwhelm PI
    review_sessions_per_week: 2

  # Scheduler optimizes within these constraints
  optimization: "maximize expected_information_gain per unit resource"
```

---

# Confidence System (Time-Decayed)

Every object stores confidence AND freshness.

```
0.0
↓
Unknown
↓
0.3
↓
Weak Evidence (retrieved > 6 months ago)
↓
0.6
↓
Likely (retrieved < 3 months ago, single source)
↓
0.8
↓
Strong Evidence (retrieved < 1 month, multiple sources)
↓
1.0
↓
Verified (replicated, cross-validated, < 2 weeks old)
```

**Confidence changes after every experiment AND every search.**
**Nothing is permanently true. Everything has a half-life.**

---

# Research Loop (Network-First)

```
Observe (Live Search for latest state of field)
    ↓
Collect Knowledge (from network, not just memory)
    ↓
Update World Model (with temporal decay applied)
    ↓
Generate Questions (based on freshest knowledge)
    ↓
Discover Gaps (verified against live literature)
    ↓
Analyze Root Causes (with temporal context)
    ↓
Generate Hypotheses
    ↓
Evaluate Ideas (with collaboration input)
    ↓
Plan Experiments (resource-aware)
    ↓
Collect Evidence (experiment + live search for baselines)
    ↓
Reviewer Simulation (with freshness audit)
    ↓
Decision (human-in-the-loop gate)
    ↓
Update Memory & World Model
    ↓
Collaboration Sync (share with team)
    ↓
Repeat
```

Research continuously improves the World Model through live search.

---

# Human-in-the-Loop Gateway

## Critical Decision Gates

The system NEVER autonomously:
- Submit a paper
- Claim a result is "true"
- Allocate significant resources
- Change research direction without human approval

## Gate Points
```yaml
decision_gates:
  - gate: "hypothesis_commitment"
    trigger: "Hypothesis confidence > 0.7"
    human_action: "Approve / Modify / Reject hypothesis"
    ai_provides: "Supporting evidence, contradicting evidence, confidence trajectory"

  - gate: "experiment_authorization"
    trigger: "Experiment plan ready"
    human_action: "Approve resource allocation / Request modifications"
    ai_provides: "Expected cost, expected information gain, risk assessment"

  - gate: "direction_pivot"
    trigger: "Evidence contradicts core assumption"
    human_action: "Pivot / Persist / Explore alternatives"
    ai_provides: "Alternative hypotheses, sunk cost analysis, opportunity cost"

  - gate: "submission_readiness"
    trigger: "Reviewer score > threshold AND freshness audit passed"
    human_action: "Submit / Revise / Abandon"
    ai_provides: "Reviewer report, weakness analysis, comparison to latest SOTA"
```

## Explainability Requirement
At every gate, the AI must provide:
1. **What** it recommends
2. **Why** (reasoning chain with evidence links)
3. **How confident** it is
4. **What could go wrong** (red team perspective)
5. **What has changed** since last gate (temporal delta)

---

# Integration with External Tools

## Academic APIs
- Semantic Scholar API (200M+ papers, citation graphs, TLDR)
- arXiv API (preprints, RSS feeds)
- PubMed API (biomedical)
- OpenAlex API (open academic graph)
- CrossRef API (DOI resolution)

## Web Search
- General web search for breaking news, blog posts, conference announcements
- GitHub search for code implementations
- Hugging Face search for models and datasets

## Execution Environment
- IPython for data analysis and visualization
- Git integration for version control
- Docker for reproducible experiments
- Cloud compute APIs for large-scale experiments

## Collaboration Tools
- Shared blackboard (Redis/PostgreSQL backend)
- Notification system (email/Slack/Discord)
- Version-controlled research logs (Git-based)

---

# Research Objectives

Research contributions may include:
- New Problems
- New Insights
- New Theories
- New Algorithms
- New Architectures
- New Training Strategies
- New Optimization Methods
- New Inference Methods
- New Benchmarks
- New Datasets
- New Evaluation Metrics
- New Systems
- New Applications

The operating system is domain-independent.

**NEW: Temporal Contributions**
- Identifying when established methods become obsolete
- Predicting emerging research directions
- Timing research to align with field maturity curves

---

# Mission Statement

Research OS 2.1 is a state-driven, evidence-based, time-aware, network-first, 
collaboration-native cognitive operating system.

Its mission is not to maximize publications.

Its mission is to maximize understanding — and understanding requires knowing 
what is true NOW, not what was true at training time.

Better understanding produces better questions.
Better questions produce better research.
Better research produces meaningful scientific progress.

And progress is a collective endeavor — the OS is designed for teams, 
not lone researchers.
