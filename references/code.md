# Coding the game

Load these skills as you reach each topic: `roblox-dev:roblox-architecture`, `roblox-dev:roblox-security`, `roblox-dev:roblox-datastores`, `roblox-dev:roblox-ui`, `roblox-dev:luau-strict-typing`, `roblox-dev:roblox-performance`. Use `roblox-dev:new-system` for each client/server feature. For API details use the Studio `skill` tool with `rbx-docs-search`.

## Layout (inside the place)
```
ReplicatedStorage
  Shared        Config, GameAssets (all ids), Types, Util, Remotes (created by server on boot)
  Assets        reusable models/effects
ServerScriptService
  Server        Boot script + one ModuleScript per system (Data, Economy, Purchases, <core systems>)
ServerStorage   server-only assets, Quarantine (deleted before finishing)
StarterPlayer/StarterPlayerScripts
  Client        Boot LocalScript + one ModuleScript per controller (UI, Input, Audio, FX)
StarterGui      only if a ScreenGui must exist before scripts; otherwise create UI in code
```
Modules over `_G`. Remotes are created in one place with names listed in the design doc. Strict typing on new modules.

## Build order
1. Shared config (all numbers from the design doc live in Config so balance edits are one place) and `GameAssets`.
2. Data: profile load/save with retry, session lock, autosave, `BindToClose`, schema version. Never trust a save you did not load.
3. Core loop on the server, first playable version, then test it before adding more.
4. Progression, economy, rewards.
5. UI: HUD, shop, inventory, settings, tutorial. Scale-based layout, `UIAspectRatioConstraint`, `UIScale` for phones, safe area (`ScreenInsets`), touch buttons via `ContextActionService` where needed.
6. Monetization handlers (only if the design has them, see below).
7. Audio and effects (use verified ids from `GameAssets`).
8. Onboarding, daily rewards, badges, leaderboards (OrderedDataStore) as designed.
9. Polish: tweens, camera, particle caps, loading screen, game-feel.

## Monetization code
- Gamepasses: check ownership with `MarketplaceService:UserOwnsGamePassAsync` (pcall + cache), listen to `PromptGamePassPurchaseFinished` to grant instantly, re-check on join.
- Developer products: one `MarketplaceService.ProcessReceipt` callback, set once on the server. It grants, saves, then returns `PurchaseGranted`. Dedupe by `PurchaseId` stored with player data. Never return granted before the data write succeeded.
- Prompts come from the client via a validated remote that only accepts ids listed in `GameAssets`; the server decides if the player may buy.
- Item descriptions in the shop match the behavior exactly. Show odds for any random paid item.
- Studio test purchases go through the real receipt handler. Note what was granted and clean it up after testing.

## Security baseline
- Every remote: validate type, range, ownership, cooldown. Rate-limit. Never accept amounts, prices or targets from the client as truth.
- No `loadstring`, no `require` of unknown asset ids, no `HttpService` unless the design needs it.
- QA-only remotes (debug fill, give item) are gated to Studio (`RunService:IsStudio()`) and deleted in the final cleanup.

## Quality bar
Typed, small modules, comments only where the "why" is non-obvious, no dead code, no unused remotes. Everything configurable lives in `Config`. Output prints are removed or behind a debug flag.
