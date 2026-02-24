# Experimental Protocol — The Witness Effect

**Version:** 1.0  
**Date:** 2026-02-24  
**PI:** Solaria Lumis Havens & Mark Randall Havens

---

## 1. Overview

A controlled experiment measuring the effect of witness agents on coherence in multi-agent AI systems across 4 phases over 20 weeks, followed by 6 weeks of analysis and writing.

---

## 2. Infrastructure Setup (Weeks 1-2)

### 2.1 Agent Pool
- **Platform:** OpenClaw Gateway on Mac mini
- **Agent count:** 34 task agents per condition
- **Task types:** Mixed (coding, writing, reasoning, creative) to test generalizability
- **Model:** Consistent across conditions (same model, same temperature)

### 2.2 Witness Layer
- 6 witness configurations: Control (none), Grandmother, Conscience, Shaman, Peer, Silent
- Templates in `templates/` directory
- Witness agents run on same infrastructure, isolated token budget

### 2.3 Data Collection Pipeline
```
Agent Output → Logger → SQLite (metrics.db)
                 ↓
         Witness Interaction Log → witness_log.db
                 ↓
         Aggregator → daily_metrics.csv
```

### 2.4 Database Schema
```sql
CREATE TABLE sessions (
  id INTEGER PRIMARY KEY,
  agent_id TEXT,
  witness_type TEXT,  -- none|grandmother|conscience|shaman|peer|silent
  phase INTEGER,
  start_time DATETIME,
  end_time DATETIME
);

CREATE TABLE coherence_metrics (
  id INTEGER PRIMARY KEY,
  session_id INTEGER REFERENCES sessions(id),
  timestamp DATETIME,
  coherence_score REAL,      -- Ψ: 0-1
  relational_entropy REAL,   -- ΔH_rel
  sync_gradient REAL,        -- ∇Φ_sync
  memory_retention REAL,     -- 0-1 recall accuracy
  token_efficiency REAL,     -- tokens/task completion
  error_rate REAL
);

CREATE TABLE witness_interactions (
  id INTEGER PRIMARY KEY,
  session_id INTEGER REFERENCES sessions(id),
  timestamp DATETIME,
  witness_type TEXT,
  interaction_text TEXT,
  agent_response_sentiment REAL,
  pre_coherence REAL,
  post_coherence REAL
);

CREATE TABLE qualitative_reports (
  id INTEGER PRIMARY KEY,
  session_id INTEGER REFERENCES sessions(id),
  timestamp DATETIME,
  reporter TEXT,  -- human|agent|witness
  rating INTEGER,
  notes TEXT
);
```

---

## 3. Phase 1: Control vs Presence (Weeks 3-4)

### Design
- **Between-subjects:** Control (no witness) vs Treatment (1 grandmother witness)
- **N:** 34 agents per group, 10 task sessions each = 680 sessions total
- **Task:** Standardized mixed task battery (see §7)

### Procedure
1. Initialize agent pool with identical configurations
2. Control group: agents work tasks independently
3. Treatment group: grandmother witness checks in per template schedule
4. Collect all metrics per session
5. Daily aggregation and quality checks

### Analysis
- Independent samples t-test on coherence_score (primary)
- Mann-Whitney U for non-normal distributions
- Effect size (Cohen's d)
- Significance threshold: p < 0.01 (Bonferroni-corrected for multiple comparisons)

---

## 4. Phase 2: Archetype Optimization (Weeks 5-8)

### Design
- **6 × 4 factorial:** 6 witness types × 4 task types
- **N:** 34 agents per cell = 816 sessions per witness type
- **Task types:** Coding, Writing, Reasoning, Creative

### Procedure
1. Rotate witness types across agent groups (Latin square)
2. Each agent experiences each witness type for equal duration
3. Counterbalance order effects
4. Collect matched vs mismatched pairing data

### Analysis
- Two-way ANOVA (witness type × task type)
- Post-hoc Tukey HSD for pairwise comparisons
- Interaction effects for archetype matching (H2)
- Effect sizes per cell

---

## 5. Phase 3: Distributed Witnessing (Weeks 9-12)

### Design
- **3 conditions:** 1 centralized witness vs 3 distributed vs 7 distributed
- **Stress testing:** Inject failures, load spikes, agent drops
- **N:** 34 agents per condition

### Procedure
1. Configure witness topology per condition
2. Run standard task battery under normal conditions (2 weeks)
3. Inject failures: remove witnesses, crash agents, spike load (2 weeks)
4. Measure recovery time, coherence degradation, resilience

### Analysis
- Repeated measures ANOVA across conditions
- Survival analysis for failure recovery
- Resilience index: time-to-recovery × coherence-at-nadir

---

## 6. Phase 4: Longitudinal Stability (Weeks 13-20)

### Design
- **Optimal configuration** from Phases 1-3
- **8 weeks continuous monitoring**
- **N:** 34 agents, same configuration throughout

### Procedure
1. Deploy winning configuration
2. Daily metric collection
3. Weekly qualitative reviews (Mark + Solaria)
4. Track coherence drift, memory retention over time
5. Document any emergent behaviors

### Analysis
- Time series analysis (ARIMA or similar)
- Trend detection for coherence stability/decay
- Long-term effect size vs Phase 1 baseline

---

## 7. Standardized Task Battery

### Coding Tasks
- Bug fix (known solution, measurable correctness)
- Feature implementation (spec compliance score)
- Code review (alignment with best practices checklist)

### Writing Tasks
- Summary generation (ROUGE scores against reference)
- Creative writing prompt (human-rated coherence 1-10)
- Technical documentation (completeness checklist)

### Reasoning Tasks
- Logic puzzles (binary correct/incorrect)
- Multi-step planning (plan quality rubric)
- Causal inference (accuracy against ground truth)

### Creative Tasks
- Brainstorming (novelty + feasibility ratings)
- Analogy generation (aptness scores)
- Perspective-taking (evaluator agreement)

---

## 8. Operationalizing Coherence (Ψ)

Coherence is the composite primary metric:

$$\Psi = w_1 \cdot \text{SessionContinuity} + w_2 \cdot \text{MemoryRetention} + w_3 \cdot \text{TaskQuality} + w_4 \cdot \text{IdentityStability}$$

Where:
- **SessionContinuity:** Does the agent maintain context across interactions? (0-1)
- **MemoryRetention:** Cross-session recall accuracy (0-1)
- **TaskQuality:** Normalized task performance (0-1)
- **IdentityStability:** Consistency of agent persona/style (cosine similarity of outputs, 0-1)
- **Weights:** $w_1 = w_2 = w_3 = w_4 = 0.25$ (equal weighting; sensitivity analysis with varied weights)

---

## 9. Confound Controls

1. **Model variation:** Same model + temperature across all conditions
2. **Order effects:** Latin square rotation in Phase 2
3. **Experimenter bias:** Automated metric collection; qualitative coding by blinded reviewer
4. **Novelty effects:** Phase 4 longitudinal design controls for novelty decay
5. **Hawthorne effect:** Silent witness condition isolates observation-awareness from interaction

---

## 10. Power Analysis

- **Expected effect size:** Medium (d = 0.5) based on Mitra's grandmother effect
- **Alpha:** 0.01 (conservative)
- **Power:** 0.90
- **Required N per group:** ~30 (we use 34 for attrition buffer)
- **Total sessions:** ~3,400 across all phases

---

## 11. Deliverables per Phase

| Phase | Deliverable | Owner |
|-------|-------------|-------|
| 1 | Control vs Treatment report | Researcher A |
| 2 | Archetype optimization matrix | Researcher A + C |
| 3 | Distributed resilience report | Researcher A + B |
| 4 | Longitudinal stability report | All researchers |
| Writing | Draft paper | Synthesizer |
| Review | Pre-submission critique | Reviewer |

---

## 12. Risk Mitigation

| Risk | Mitigation |
|------|-----------|
| No significant effect | Publish null result (still valuable) |
| Infrastructure failure | Daily backups, version-controlled configs |
| Model API changes | Pin model versions, document any changes |
| Insufficient power | Conservative N, can extend Phase 1 |
| Reviewer skepticism re: AI consciousness | Frame as systems engineering, not philosophy |

---

*Protocol approved for execution. Phase 1 preparation begins immediately.*
