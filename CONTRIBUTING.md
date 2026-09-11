# Contributing to FeTVControl

This is a personal portfolio project but structured as if it were a real program. These guidelines keep the repo clean and tell the story correctly.

---

## Branch Strategy

```
main          ← stable, phase-complete code only
dev           ← active development
phase/N-name  ← feature branches per phase (e.g. phase/2-lowfi-model)
```

Work on a `phase/N` branch and merge to `dev` when phase exit criteria are met. Merge `dev` → `main` when a phase is complete and validated.

---

## Commit Message Format

Use conventional commits:

```
type(scope): short description

Types: feat, fix, docs, refactor, test, chore
Scope: phase1, phase2, ..., docs, sim_harness, rust, fdir

Examples:
  feat(phase2): implement BLDC motor electrical model
  docs(phase1): fill in motor parameter estimates from REF-002
  fix(phase4): correct backlash deadband sign convention
  test(phase6): add jam fault injection test case
```

---

## File Organization Rules

- Python source goes in `python/` subdirectories, notebooks in `notebooks/`
- Rust source goes in `rust/` subdirectories as proper Cargo crates
- All generated plots go in `results/phaseN/` — never commit generated plots to source directories
- Data files go in `phase3_validation/data/` with a `README.md` describing provenance
- No hardcoded paths — use relative paths or config files

---

## Documentation Standard

Every phase directory should have a `README.md` that answers:
1. What does this code do?
2. How do I run it?
3. What are the key results / what should I see?
4. What are known limitations?

---

## Phase Completion Checklist

Before merging a phase branch to `dev`:
- [ ] All exit criteria in `docs/phase_roadmap.md` checked off
- [ ] Phase `README.md` written
- [ ] Key result plots saved to `results/phaseN/`
- [ ] Any new TBDs or open items added to requirements or roadmap docs
- [ ] Code runs clean from a fresh environment (document dependencies)
