# Three Cubes

## The Golden Path

**One quality bar, applied the same way across every repo.** The Golden Path is the paved road every Three Cubes repo's quality gate, CI, and deploy derive from — so repos don't drift into bespoke copies.

| Repo | What it is | How you consume it |
|---|---|---|
| **[tc-fitness](https://github.com/three-cubes/tc-fitness)** | The runnable **quality gate** — the `three-cubes-fitness` library + the `tc-fitness` CLI. The *what-to-check*: architecture-fitness rules, per-file baselines, the net-new-violations ratchet. | `pip install git+…tc-fitness.git@vX` + a `[tool.tc_fitness]` block in your `pyproject.toml`. |
| **[tc-pipelines](https://github.com/three-cubes/tc-pipelines)** | The reusable **pipelines** — the `workflow_call` workflows + composite actions for CI/quality **and** Azure-VM deploy. The *how-it-runs*. | `uses: three-cubes/tc-pipelines/…@v1` in your workflow. |

Consumed by **[tc-agent-zone](https://github.com/three-cubes/tc-agent-zone)** · **[kairix](https://github.com/three-cubes/kairix)** · **[kata](https://github.com/three-cubes/kata)**.

## Adopt the Golden Path in a repo

1. **Pin the gate.** Add `three-cubes-fitness @ git+https://github.com/three-cubes/tc-fitness.git@vX` to your deps + a `[tool.tc_fitness]` block declaring your gate (test dirs, coverage roots, ruff/bandit targets, the detect-secrets baseline). Run it locally with `tc-fitness run` — `make check` and CI invoke the **same** binary against the **same** config, so **local == CI by construction**.
2. **Add the CI caller.** `uses: three-cubes/tc-pipelines/.github/workflows/python-quality-gate.yml@v1` for Python repos, or `meta-quality-gate.yml@v1` for framework / non-Python repos.
3. **Add deploy (if you ship to a VM).** `uses: three-cubes/tc-pipelines/.github/workflows/azure-vm-deploy.yml@v1`. See [`tc-pipelines/docs/MIGRATION.md`](https://github.com/three-cubes/tc-pipelines/blob/main/docs/MIGRATION.md).

**Always pin a tag — never `@main`.** Consumers pin `@v1` (the floating major); the engine pins a `@vX` release.
