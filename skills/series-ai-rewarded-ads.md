---
name: Add rewarded-ad monetization
description: Serve a rewarded video ad with the RUN.world SDK and grant an in-game reward only when the ad completes, with correct error handling across the JS bridge.
api: RUN.world SDK
method: generated
generated: '2026-07-21'
source: https://github.com/series-ai/venus-sdk-docs (README.md, api/ADS.md, error-handling.md)
operations:
- RundotGameAPI.ads.showRewardedAdAsync
- RundotGameAPI.appStorage.setItem
- RundotGameAPI.analytics.recordCustomEvent
---

# Add rewarded-ad monetization

Serve a rewarded video through the RUN.world SDK and grant the reward only when
the ad reports completion.

## Steps

1. Import the API: `import RundotGameAPI from '@series-inc/rundot-game-sdk/api'`.
2. On the player's "watch for reward" action, call the ad:
   ```typescript
   try {
     const result = await RundotGameAPI.ads.showRewardedAdAsync()
     if (result /* completed per api/ADS.md */) {
       await RundotGameAPI.appStorage.setItem('coins', String(coins + 50))
     }
   } catch (err) {
     // ad failed to load/show — degrade gracefully, do NOT grant the reward
   }
   ```
3. Record the outcome (fire-and-forget, always `.catch()`):
   `RundotGameAPI.analytics.recordCustomEvent('rewarded_ad', { placement: 'shop' }).catch(() => {})`.

## Rules

- Every SDK call can reject across the JS bridge; an unhandled rejection is a fatal RUNTIME_ERROR that crashes the game. Wrap awaited calls in try/catch.
- Grant rewards only on confirmed completion; never assume success.
- Ad calls are rate-limited (429 -> RateLimitedError); keep the game playable if an ad is unavailable. See errors/series-ai-problem-types.yml.
