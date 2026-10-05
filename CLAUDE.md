# okto-pulse-benchmarks — persistent state

This file exists so status survives across sessions and machines, same
convention as `caura-ai`'s sibling benchmark repo this project was modeled
on. Keep it current; the README is the summary, this is the detail.

## Current status (2026-10-05)

One suite populated: `suites/swe_bench_lite`. 10 of SWE-bench Lite's 300
instances run, all `pytest-dev/pytest`, two arms (`direct`, `staged`), three
models run so far:

- **`qwen3.8-flash`** — harness-graded resolved rate: staged 10/10, direct
  9/10. Full numbers and caveats: `reports/2026-09-18-swe-bench-lite-pilot.md`.
- **`Qwen3.5-9B`** — code-change completion only (not harness-graded): both
  arms 10/10 applied a code change; staged lifecycle completed
  ideation/refinement/spec/task for all 10, validation gate 0/10. Full
  writeup: `reports/2026-09-23-qwen3.5-9b-pytest-pilot.md`.
- **`Qwen3.5-27B`** — harness-graded resolved rate: staged 10/10, direct
  10/10, no discordant pair. Staged arm's architecture gate was manually
  verified rather than server-transitioned (live Pulse `ideation -> done`
  `500` bug on ideations with an attached architecture); per-instance diffs
  not preserved for this run, only raw harness reports. Full writeup:
  `reports/2026-10-05-qwen3.5-27b-pytest-pilot.md`.

Headline, restated: staged (Okto Pulse's plan-then-edit lifecycle) has never
done worse than direct across any of the three models run so far, and
closed the one gap direct left under `qwen3.8-flash` — 9/10 → 10/10.

## Source of the raw run

The original run lived in a scratch workspace
(`~/Infrasity/bench-workspace`) before being promoted into this repo. That
scratch tree also has the per-task work directories (`qwen-direct/`,
`qwen-staged/`, `wt/`) and full container logs
(`logs/run_evaluation/<run_id>/`) that were **not** copied here — too large
to commit, and reproducible from `results/predictions_*.jsonl` via the
commands in `suites/swe_bench_lite/README.md`.

A companion scratch dir (`~/Infrasity/swe-bench-workspace`) holds a
single-instance gold-patch validation (`pytest-dev__pytest-5221`) that
predates the 10-instance sanity run now in
`results/gold.validate-gold-all-10.json`; superseded, not copied here.

## Open items, in priority order

1. **Scale past 10 instances / past one repo.** Cheapest next step is more
   `pytest` instances (harness already proven here); after that, Lite's
   other 11 repos (Django, Flask, scikit-learn, sympy, matplotlib, requests,
   astropy, sphinx, pydata/xarray, pylint, and more).
2. **Add paired significance testing** once n supports it — bootstrap CI /
   exact McNemar, not a raw count. At n=10 the best-case McNemar result
   (this pilot's 1 discordant pair) still doesn't clear significance, so
   this repo makes no significance claim yet; don't let a future update
   assert one without the math actually supporting it.
3. **Fix the `ideation -> done` `500` on ideations with an attached
   architecture design**, then re-run the Qwen3.5-27B staged arm fully
   server-gated instead of relying on manual verification of that gate.
4. **Preserve per-instance diffs for the Qwen3.5-27B run**, same as the
   `qwen3.8-flash` and Qwen3.5-9B runs, so a worked-example comparison is
   possible for it too.
5. **Run additional agent models** on the same instance set to check
   whether the staged advantage generalizes beyond `qwen3.8-flash` — ideally
   one that produces a discordant pair, since a clean sweep on both arms
   (as with `Qwen3.5-27B`) can't show a staged advantage even if one exists.

## Conventions carried over from the reference repo this was modeled on

- Every comparison is **paired**: same instances, both arms, same harness
  run.
- A raw delta is a lead, not a finding, until a sample size supports a
  significance test.
- Caveats go **before** the numbers they qualify, not as a footnote after.
