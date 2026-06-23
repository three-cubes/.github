# Three Cubes

## How Three Cubes repos work

Every Three Cubes repo runs the **same** quality check and the **same** CI and deploy steps. We used to hand-copy those into each repo, and the copies drifted apart. Now there is one shared quality check and one shared set of CI steps, and every repo uses them. You run the check on your laptop and get the exact result CI will give — no "works on my machine", and one fix to the shared check improves every repo at once.

## The two shared pieces

| Piece | What it is | How your repo uses it |
|---|---|---|
| **[tc-fitness](https://github.com/three-cubes/tc-fitness)** | The quality check itself — a program you run. It runs your linters, type-check, tests, coverage, security scan, and architecture rules, and gives you **one** pass/fail. | Your repo declares **what** to check in a `[tool.tc_fitness]` block in `pyproject.toml`, and locks it to a specific version. tc-fitness knows **how** to run it. |
| **[tc-pipelines](https://github.com/three-cubes/tc-pipelines)** | The CI and deploy steps every repo's GitHub Actions calls. | `uses: three-cubes/tc-pipelines/…@v1` in your workflow, so CI runs the exact same check you run locally. |

Used today by **[tc-agent-zone](https://github.com/three-cubes/tc-agent-zone)** · **[kairix](https://github.com/three-cubes/kairix)** · **[kata](https://github.com/three-cubes/kata)**.

## How merging works

- **Green merges itself.** When your PR's checks pass, it merges on its own. Routine work does not wait for a human reviewer.
- **Red is never bypassed.** If checks fail, you fix the change. You do not force it in — force-merging over a red check is exactly what breaks the main branch.
- **Only changes to the checks themselves need a human.** Changing the quality check or the CI config (the files that define how everything is checked) requires one human approval. This stops anyone — person or agent — from quietly weakening the check that protects every repo.
- **Agents work under a bot identity.** Agents open PRs as the `three-cubes-agent` GitHub App, so authorship is clean and the work can be reviewed when needed.

## Start here

- **New here / want the overview?** Read **[STANDARDS.md](https://github.com/three-cubes/tc-pipelines/blob/main/governance/STANDARDS.md)** — the one canonical index that links to everything authoritative.
- **Setting up or building a repo?** Add the quality check and lock it to a version: put `three-cubes-fitness @ git+https://github.com/three-cubes/tc-fitness.git@vX` in your deps and a `[tool.tc_fitness]` block in `pyproject.toml`. Then add the CI caller: `uses: three-cubes/tc-pipelines/.github/workflows/python-quality-gate.yml@v1`. See **[tc-pipelines/docs/MIGRATION.md](https://github.com/three-cubes/tc-pipelines/blob/main/docs/MIGRATION.md)**. Always lock to a tag — never `@main`.
- **An agent?** Each repo's `CLAUDE.md` / `AGENTS.md` points you at **[STANDARDS.md](https://github.com/three-cubes/tc-pipelines/blob/main/governance/STANDARDS.md)** before you touch CI, the quality check, or governance. Read it first; improve the canonical standard instead of forking your own.

## The daily loop

1. Make your change on a branch.
2. Run the check locally the same way CI does. Install the full dev environment, then run both commands and get them green before you push:

   ```bash
   uv sync --all-extras --all-groups
   uv run pre-commit run --all-files
   uv run tc-fitness run
   ```

3. If it is green locally but red in CI, that is a bug in the local setup — fix the setup, do not force the merge.
4. If you changed an input to a generated file (for example `.github/CODEOWNERS`), regenerate the generated file and commit it in the **same** change.
5. Open a PR. Green merges itself. If it touches a file that defines the checks or CI, one human approves first.

## Where to go next

| Go here | For |
|---|---|
| **[STANDARDS.md](https://github.com/three-cubes/tc-pipelines/blob/main/governance/STANDARDS.md)** | The canonical index — links to every authoritative standard. |
| **[tc-fitness](https://github.com/three-cubes/tc-fitness)** | The quality check (run it, add new checks here). |
| **[tc-pipelines](https://github.com/three-cubes/tc-pipelines)** | The shared CI and deploy steps. |
