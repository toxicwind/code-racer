# AGENTS.md — code-racer

> Refines: `/home/toxic/AGENTS.md` (the estate control-plane). This file is
> **living doctrine** for the codeflux racing family.

## Living code (2026-11-04, architect directive)

**This harness is "living code" — agents MAY modify, mutate, and add to it as
they use it.** code-racer exists to make strategy selection empirical: register
N solvers, race them on a real task with real pytest acceptance tests, and the
first PASSING solution wins. Extend it freely.

- **Add tasks**: drop a `task/` dir with `task.md` + `tests/` + `limits.yaml`.
  The task is the contract — write the acceptance tests first (TDD).
- **Add strategies**: append to `strategies/registry.json`; a solver writes
  `<outdir>/solution.py`. Race with `race-task <task> --strategies all`.
- **Mutate the harness**: if the loader, runner, or ledger is missing a
  capability (per-hop latency, new limits, a different acceptance signal),
  fix it here — root cause, real committed file.
- **First-passing-wins is the law**: validity = exit 0 AND `RACE-PASS` on
  stdout, only emitted when pytest passes. Slow is a kind of wrong; broken is
  disqualified. Never relax this to "first non-crashing".
- **Record winners**: every race appends to `winners/code-racer-<task>.jsonl`
  (fsync'd) + the global HFT log. The ledger is the provenance.

## Build / run

```bash
python3 race-task <task-dir> --strategies all   # race on a task
python3 bin/race.py ...                          # HFT first-valid-wins racer
python3 bin/measure.py ...                       # latency measurement
```

## Conventions

- Python. `task_loader.py` / `run_candidate.py` / `registry.py` / `winners.py`
  are the seams. Keep them thin.
- Tasks are real subprocesses in isolated testbeds; tests are real pytest.
  No mocks, no vibes.
- Commit imperative mood, scope prefix (`task:`, `harness:`, `strategies:`,
  `docs:`).

## Don't

- Don't make validity a "did it run" check — it must pass the acceptance tests.
- Don't cache winners across races (stale strategy ranking is worse than none).
- Don't add a network call to the harness itself (it wraps subprocesses).