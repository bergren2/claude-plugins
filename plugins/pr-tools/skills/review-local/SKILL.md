---
name: review-local
description: Run a local code review of the current branch inside the claude-sandbox Dev Container
argument-hint: <base ref to diff against, e.g. origin/main — optional>
---

Run a local, sandboxed code review of the current branch using the [`claude-sandbox`](https://github.com/bergren2/claude-sandbox) Dev Container, which runs Claude Code with `--dangerously-skip-permissions` behind an outbound network firewall.

The review runs **inside the container**. The preferred path drives the container headlessly from the host via the Dev Containers CLI — no VS Code, no manual steps. `--dangerously-skip-permissions` only ever executes inside the firewalled container (via `devcontainer exec`), never on the host, so this stays compliant with the "never skip permissions on the host" rule.

## Step 1 — Confirm this is a git repo

Run `git rev-parse --show-toplevel`. If it fails, stop and tell the user this skill must be run from inside a git repository.

Resolve the base ref: use $ARGUMENTS if provided, otherwise `origin/main`.

## Step 2 — Check host prerequisites

The headless path needs Docker running and the Dev Containers CLI:

- `docker info` succeeds → Docker is running. If not, tell the user to start Docker Desktop.
- `devcontainer --version` succeeds → CLI present. If not, tell the user to install it: `npm install -g @devcontainers/cli`.

If either is missing and the user can't install it right now, skip to the **Fallback** section instead of failing outright.

## Step 3 — Check for the sandbox scaffolding

Check whether the repo root contains all of:

- `.devcontainer/Dockerfile`
- `.devcontainer/devcontainer.json`
- `.devcontainer/init-firewall.sh`
- `scripts/review.sh`
- `scripts/review-host.sh`

If all are present, skip to Step 5.

## Step 4 — Bootstrap if missing

If any scaffolding file is missing, tell the user and offer to bootstrap. The bootstrap script pulls the Dev Container files and review scripts from claude-sandbox into the current repo. It requires `bun`.

```powershell
bun ~/.claude/scripts/bootstrap-claude-sandbox.ts [targetDir] [--force] [--ref <branch|tag|sha>]
```

- `targetDir` defaults to the current directory.
- Existing files are skipped unless `--force` is passed, so it is safe to re-run.

If `~/.claude/scripts/bootstrap-claude-sandbox.ts` does not exist (e.g. the plugin is installed on a machine without the user's dotfiles), point the user at https://github.com/bergren2/claude-sandbox to copy `.devcontainer/` and `scripts/` manually, then stop.

## Step 5 — Run the review (headless)

Run the host driver from the repo root. It brings the container up (applying the firewall on start), runs the review inside it, and writes `review.md` back into the working tree:

```bash
scripts/review-host.sh <base ref>
```

Pass the base ref from Step 1. To also post the result to the branch's PR, prefix `POST_COMMENT=true`.

Notes:
- The **first** run builds the Docker image and can take several minutes — this is expected, not a hang. Run with a generous timeout (or in the background) and tell the user it's building. Later runs reuse the image and are fast.
- Auth inside the container comes from `ANTHROPIC_API_KEY` on the host (forwarded automatically) or a prior `claude` login persisted in the `claude-sandbox-config` volume. If the run fails on auth, tell the user to set `ANTHROPIC_API_KEY` or log in once inside the container.

## Step 6 — Report

Read `review.md` from the repo root and summarize the findings inline for the user (most severe first). Mention where the full file is (`review.md`) and whether it was posted to a PR.

## Fallback — VS Code, if the CLI/Docker path is unavailable

If Docker or the Dev Containers CLI isn't available, hand off the manual flow and stop:

1. Open the repo in VS Code.
2. Command Palette → **Dev Containers: Reopen in Container**. Wait for firewall init to finish.
3. In the container's integrated terminal (a Linux shell — bash syntax even on Windows):

   ```bash
   scripts/review.sh <base ref>           # writes review.md
   POST_COMMENT=true scripts/review.sh    # also post to the PR
   ```

## Never on the host

Do not run `claude --dangerously-skip-permissions` or `scripts/review.sh` directly on the host — `review.sh` is firewall-dependent and only safe inside the container. The host only ever runs `scripts/review-host.sh` (which orchestrates the container) or the bootstrap script.
