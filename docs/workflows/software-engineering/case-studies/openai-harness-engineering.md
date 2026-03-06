# OpenAI Harness Engineering: Lessons Learned

Case study on building software with AI agents - learnings from OpenAI's Codex experiment.

---

## The Experiment

OpenAI built an internal product with **0 lines of manually-written code** over 5 months:

| Metric | Result |
|--------|--------|
| Duration | 5 months |
| Code volume | ~1 million lines |
| Engineers | 3 → 7 |
| PRs merged | ~1,500 |
| Throughput | 3.5 PRs/engineer/day |

**Key constraint**: Human time and attention is the scarce resource.

---

## Core Philosophy

```
Humans: steer (design, guide, validate)
Agents: execute (write code, tests, config)
```

This aligns with our approach: humans handle planning, agents execute.

---

## Key Learnings

### 1. Environment Over Instructions

**Problem**: A giant AGENTS.md fails because:
- Context becomes a scarce resource
- Too much guidance = no guidance
- Documentation rots instantly
- Hard to verify

**Solution**: Treat AGENTS.md as a **table of contents**, not an encyclopedia.

```
AGENTS.md → points to →
  ├── docs/ (system of record)
  ├── design-docs/
  ├── exec-plans/
  └── references/
```

**Our parallel**: Techno expertise courses are the "docs/" - structured, deep knowledge.

---

### 2. Agent Legibility

**Problem**: Agents can't access what they can't see in context.

| What Codex Can See | What It Can't |
|-------------------|---------------|
| Code in repo | Google Docs |
| Markdown files | Slack messages |
| Schemas | Tacit human knowledge |
| Versioned artifacts | External context |

**Solution**: Push more context into the repository. If it's not in-repo, it doesn't exist for the agent.

**Our parallel**: `.technos/` with complete courses - everything the agent needs is accessible.

---

### 3. Architecture as Prerequisite

**Problem**: Agent throughput exceeds human attention. Without constraints, code quality degrades.

**Solution**: Rigid layered architecture enforced mechanically:

```
Types → Config → Repo → Service → Runtime → UI
```

Cross-cutting concerns (auth, telemetry) enter through "Providers" only.

**Enforced by**: Custom linters, structural tests.

**Key insight**: Architecture you'd normally postpone until "hundreds of engineers" becomes essential early with agents.

**Our parallel**: Contract tests enforce interface compliance. System prompt defines structure.

---

### 4. Observability for Agents

**Problem**: How does the agent verify its work?

**Solution**: Give Codex its own observability stack:
- Logs accessible via LogQL
- Metrics via PromQL
- Traces via TraceQL
- Chrome DevTools for UI validation

**Result**: Single runs can work 6+ hours, validating fixes autonomously.

**Our parallel**: CI runs contract tests, validates coverage, ensures quality.

---

### 5. Entropy and Garbage Collection

**Problem**: Agents replicate existing patterns - even suboptimal ones. Technical debt compounds.

**Human-first approach** (fails):
- "AI slop" cleanup day (20% of Friday)
- Doesn't scale

**Agent-first solution**:
- "Golden principles" encoded as rules
- Recurring background tasks scan for deviations
- Automated refactoring PRs

**Key insight**: Technical debt is like high-interest debt. Pay continuously, not in painful bursts.

**Our parallel**: KPIs track quality. Metrics identify issues. Continuous improvement.

---

### 6. Merge Philosophy Changes

**Traditional**:
- Block on strict review gates
- Fix every test flake immediately
- Long-lived PRs

**Agent-first**:
- Minimal blocking gates
- Test flakes = re-run, not block
- Short-lived PRs

**Why**: Corrections are cheap, waiting is expensive when agent throughput > human attention.

---

## What Agents Produce

In this model, agents write:

- Product code and tests
- CI configuration
- Internal tooling
- Documentation
- Review responses
- Scripts that manage the repo itself
- Production dashboards

**Humans** work at a different layer:
- Prioritize work
- Translate feedback into acceptance criteria
- Validate outcomes
- Identify missing capabilities

---

## End-to-End Autonomy

The system crossed a threshold where Codex can now:

1. Validate current state
2. Reproduce a reported bug
3. Record failure video
4. Implement fix
5. Validate via UI
6. Record resolution video
7. Open PR
8. Respond to feedback
9. Detect build failures
10. Remediate or escalate
11. Merge

---

## Comparison to Our System

| OpenAI | Our System |
|--------|------------|
| AGENTS.md = TOC | Techno expertise = structured knowledge |
| Chrome DevTools for UI | Contract tests for validation |
| Custom linters | CI + metrics |
| Golden principles | Quality standards + KPIs |
| End-to-end autonomy | Developer agent executes |

---

## Key Principles We Share

### 1. Humans Steer, Agents Execute

| What Humans Do | What Agents Do |
|----------------|---------------|
| Plan | Implement |
| Validate | Test |
| Prioritize | Write code |
| Design | Execute |

### 2. Structure Enables Autonomy

Without:
- Clear interfaces
- Enforced architecture
- Quality gates

Agents become unreliable. Structure is prerequisite, not afterthought.

### 3. Metrics Over Process

Invest in measuring outcomes, not adding process steps. Track:
- Throughput
- Quality
- Error rates
- Review cycles

### 4. Continuous Cleanup

Technical debt compounds. Address continuously:
- Daily small improvements
- Automated scanning
- Encoded principles

---

## What We Can Learn

| Their Lesson | Our Application |
|--------------|-----------------|
| AGENTS.md as TOC | Keep system prompts focused |
| Push context to repo | `.technos/` is the source of truth |
| Architecture required | Contract tests + linters |
| Observability for agents | CI validates everything |
| Entropy management | KPIs + metrics tracking |

---

## Open Questions

As noted in the article:

1. **Long-term coherence**: How does architecture evolve over years in agent-generated code?

2. **Human judgment**: Where does human insight add most leverage, and how to encode it?

3. **Model improvement**: How will capabilities change as models become more capable?

---

## Quote

> "Building software still demands discipline, but the discipline shows up more in the scaffolding rather than the code."

This validates our approach: build the **system** (issues → planning → techno expertise → contract tests → developer agent), not just the code.

---

## Further Reading

- Original article: OpenAI blog on harness engineering
- Their architecture approach
- Agent-first development practices

---

*Document version: 1.0*
*Last updated: 2026-03-06*
