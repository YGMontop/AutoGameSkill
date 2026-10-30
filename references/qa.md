# QA before publishing

Do all of this and report honestly what passed, failed or could not be checked.

## Play tests
- `start_stop_play`, then `get_console_output`. Zero errors and zero warnings you caused. Use both Client and Server datamodels with `execute_luau` to inspect state.
- Walk the whole first 5 minutes as a new player: spawn, tutorial, first reward, first purchase of an upgrade, first zone change.
- Walk the golden path of every system listed in the design doc, plus edge cases: dying, rejoining, resetting, buying with too little currency, spamming a remote, leaving mid-action.
- Reset behavior (rebirth/prestige) and anything saved: leave and rejoin Studio test, verify values.
- Multiplayer: `start_stop_play` multi-client if the game depends on other players; otherwise reason about it and say it was not tested live.

## Platform checks
- Device simulator (see the Studio `skill` tool: `rbx-device-simulator-lua`) for phone, tablet, console form factors. UI must not overlap, buttons must be reachable by thumb.
- Network: `rbx-network-simulator` for a slow connection if the game has timing-sensitive actions.
- Performance: `rbx-scene-analysis` and `rbx-perf-profiling` if the map is big. Check part counts, memory, per-frame scripts, connection leaks.

## Purchases and data
- Test every pass and product once in Studio. Confirm the grant, the save, and a rejoin. Record what you granted.
- Check DataStore contents you wrote during tests and delete your own test entries.
- Confirm all ids in `GameAssets` load (`ContentProvider:PreloadAsync`) and nothing is 0 or placeholder.

## Review
Run `roblox-dev:roblox-reviewer` (remote security, DataStore safety, memory/performance, strict typing). Fix every high finding. Mention any you chose not to fix and why.

## Final cleanup
Remove QA remotes and debug panels, the `Quarantine` folder, stray test parts, unused modules, debug prints. Make sure `StreamingEnabled`, spawn, lighting and game settings (`StarterPlayer` values) are intentional. Update `GAME_BUILD.md` with final ids and results.

## Done means
It starts, the loop is playable and fun for the first session, saving works, purchases work, mobile is usable, console is clean, and the report states anything left unverified.
