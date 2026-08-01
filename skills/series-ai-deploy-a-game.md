---
name: Deploy a game to RUN.world
description: Initialize, deploy, version, and promote an HTML5 game on the RUN.world platform using the rundot CLI, including headless CI/CD with per-game API keys.
api: RUN.world SDK
method: generated
generated: '2026-07-21'
source: https://github.com/series-ai/venus-sdk-docs (getting-started.md, deploying-your-game.md, cli-reference.md)
operations:
- rundot login
- rundot init
- rundot deploy
- rundot game api-keys create
- rundot game update-tag
- rundot game copy-tag
---

# Deploy a game to RUN.world

Use the `rundot` CLI to ship and operate a title. All commands read
`game.config.prod.json` (created by `init`) when `--game-id` is omitted.

## Steps

1. Install the CLI (macOS/Linux):
   `curl -fsSL https://github.com/series-ai/rundot-cli-releases/releases/latest/download/install.sh | bash`
   then restart your terminal and verify with `rundot --help`.
2. Authenticate: `rundot login` (browser, Google account). Session is stored in `~/.rundot/`.
3. Create the game: `rundot init --name "Bricktide" --build-path ./dist --keywords "idle,strategy"`.
   This writes `game.config.prod.json` with the game ID and build path.
4. Build your game to `./dist`, then deploy: `rundot deploy` (bumps `minor` by default; use `--bump patch` for hotfixes).
5. Make it discoverable: `rundot deploy --public` to list on Explore.
6. Promote builds with tags instead of redeploying:
   `rundot game update-tag prod --version 1.4.0` or `rundot game copy-tag staging prod`.

## CI/CD (headless)

- Create a scoped key (owner login required): `rundot game api-keys create --label "GitHub Actions" --expires-in-days 90`.
- The `rk_`-prefixed secret prints once — store it as a CI secret.
- In the pipeline: `rundot login --api-key rk_...` then `rundot deploy --bump patch`.
- Key management is blocked when authenticated with an `rk_` key.

## Rules

- Publish a changelog with `rundot deploy --changelog ./CHANGELOG.md --announce` to create a moderated release note.
- See conventions/series-ai-conventions.yml and lifecycle/series-ai-lifecycle.yml.
