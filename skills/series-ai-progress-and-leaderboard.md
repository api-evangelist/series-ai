---
name: Persist player progress and post a leaderboard score
description: Save cloud-synced player progress with the RUN.world storage API and submit a competitive score to a leaderboard, handling rate limits and bridge errors.
api: RUN.world SDK
method: generated
generated: '2026-07-21'
source: https://github.com/series-ai/venus-sdk-docs (runtime-environment.md, api/STORAGE.md, api/LEADERBOARD.md, api/RATE_LIMITS.md)
operations:
- RundotGameAPI.appStorage.setItem
- RundotGameAPI.appStorage.getItem
- RundotGameAPI.requestTimeAsync
---

# Persist player progress and post a leaderboard score

Use cloud-synced per-player storage for save state and the Leaderboards API for
competitive scores.

## Steps

1. Import the API: `import RundotGameAPI from '@series-inc/rundot-game-sdk/api'`.
2. Save progress to the per-player, cloud-synced scope:
   ```typescript
   try {
     await RundotGameAPI.appStorage.setItem('level', String(level))
     const saved = await RundotGameAPI.appStorage.getItem('level')
   } catch (err) {
     if ((err as any)?.code === 'RATE_LIMITED') { /* back off with retryAfterMs */ }
   }
   ```
3. Use `RundotGameAPI.requestTimeAsync()` for authoritative server time (never trust the device clock for run windows).
4. Submit and read scores via the Leaderboards API (see api/LEADERBOARD.md) inside the same try/catch discipline.

## Rules

- Do NOT use browser `localStorage`/`IndexedDB`/cookies — they are blocked inside the game iframe. Use the SDK storage scopes:
  `appStorage` (this game), `ownerStorage` (across your titles), `sharedStorage` (cross-creator, policy-gated), `deviceCache` (anonymous per-device).
- Detect rate limits by `err.code === 'RATE_LIMITED'` and honor `retryAfterMs`.
- Every awaited call must be wrapped; an unhandled rejection crashes the game. See conventions/series-ai-conventions.yml.
