---
name: roblox-game-builder
description: Builds a complete Roblox game from a one-line idea. Interviews the user with many questions, writes a design doc for approval, has the user sign in through the integrated browser, then makes the images, gamepasses and developer products, 3D models, uploads, map and code, tests it, and publishes. Use whenever the user gives a Roblox game idea or says things like "make me a Roblox game", "build a tycoon/obby/simulator/horror game", or "turn this idea into a full game".
---

# Roblox Game Builder

Goal: the user gives a game idea; you hand back a complete, tested, published Roblox game.
Tools: Roblox Studio MCP (`mcp__Roblox_Studio__*`), the integrated browser (`mcp__Claude_Browser__*`), the shell, and the `roblox-dev:*` skills.

## Hard rules (never break these)

1. **The user signs in, never you.** Never type, read, store or log their Roblox password, 2FA codes, cookies or API keys. Open the page, tell them to sign in, wait. At the end, offer to sign out and clean up.
2. **One approval gate.** After the interview you write a design doc and the user approves it once. After that, run without asking, and stop only for: sign-in, a policy/terms dialog, spending Robux or money, publishing or making the game public, or a real blocker.
3. **Confirm before outward actions**: publishing, setting the game public, uploading under a group, accepting terms or the maturity questionnaire (the user supplies those answers). Creating passes and products inside the user's own experience is covered by the approved design doc.
4. **Honest monetization.** Every pass and product is described exactly as it behaves. No hidden odds (paid random items must show odds), no fake currency tricks, no dark patterns aimed at kids. Developer products are not for peer-to-peer Robux transfers.
5. **Server-authoritative.** All currency, damage, inventory, purchases and progress are decided on the server. Load `roblox-dev:roblox-security` before writing remotes.
6. **No unscanned Toolbox assets.** Toolbox models are allowed, but only after the script scan in `references/models-and-map.md`. Scripts inside them are deleted.
7. **Never overwrite existing work.** If the open place already has content, ask whether to build beside it, replace it, or use a new place.
8. **Verify, don't assume.** Every asset id is checked to load. Every phase ends with a check (screenshot, console output, or test run). If something failed or was skipped, say so.

## Workflow

Keep a progress file `GAME_BUILD.md` in the working directory (design doc plus a checklist of the phases below with ids and notes). Update it after each phase. If context gets long or compacts, re-read it and resume from the first unchecked phase.

### Phase 0: Preflight
- `list_roblox_studios`, `get_studio_state`. If no Studio is connected, tell the user to open Studio on a place and stop.
- Check the integrated browser works (`tabs_context`). Check Python or Node and Edge are available (needed for images).
- Look at what is already in the place (`search_game_tree`). Apply hard rule 7.

### Phase 1: Interview
Read `references/interview.md`. Ask adaptively with `AskUserQuestion`, at most 4 questions per call, skipping anything the idea already answers. Keep going until the design has no open gaps. Always put your recommended option first and let the user say "you decide".

### Phase 2: Design doc and approval
Fill `references/gdd-template.md` into `GAME_BUILD.md`: pillars, core loop, systems, full asset list (images, models, sounds), map layout, monetization price list, data to save, milestone list. Present a short summary in chat and ask for approval once (this is the single gate). Fix what they change, then go.

### Phase 3: Sign in
Read `references/creator-hub.md`. Open Creator Hub in the integrated browser, ask the user whether assets belong to their user or a group (if not already answered), and wait while they sign in. Confirm you can see their experiences.

### Phase 4: Images
Read `references/images.md`. Make the game icon, thumbnails, pass and product icons, UI art, decals and textures. Save PNGs in `assets/images/` in the working directory.

### Phase 5: Gamepasses and developer products
Create them in Creator Hub (icons from Phase 4), following the approved price list. Record every id in the progress file and in `ReplicatedStorage.Shared.GameAssets`. Do not wire purchase code until ids exist.

### Phase 6: 3D models
Read `references/models-and-map.md`. Mixed approach: Studio AI meshes for hero props, procedural models for simple structures, parts for the rest, scanned Toolbox assets where they save time. Run long generations with `async: true` and collect results with `wait_job_finished`.

### Phase 7: Upload and register assets
Upload images (`upload_image`) and any other assets, verify each loads, and store all ids in the `GameAssets` module. Nothing in game code hard-codes an id.

### Phase 8: Map
Build the world from the design doc layout: terrain, zones, spawn, lighting, atmosphere, props, collision, streaming settings. Capture and review with `screen_capture` after each zone.

### Phase 9: Code
Read `references/code.md`. Build systems in dependency order: shared config and remotes, data saving, core loop, progression, UI, monetization handlers, audio, tutorial, polish. Load the relevant `roblox-dev:*` skills (architecture, security, datastores, ui, strict typing).

### Phase 10: QA
Read `references/qa.md`. Playtest with `start_stop_play`, check console output, test mobile layout, test purchases and saving, run `roblox-dev:roblox-reviewer`, fix findings, clean up all test data and any QA-only remotes.

### Phase 11: Publish
Set title, description, icon, thumbnails, genre and device support in Creator Hub. Ask the user for the maturity questionnaire answers and whether to make it public. Confirm, then publish. Report the game link.

### Phase 12: Wrap up
Give a short report: what was built, ids created, what was tested and how, what is not verified, known limits, how to update later. Offer to sign out of Creator Hub and delete scratch files.

## Studio gotchas learned in practice
- `execute_luau` calls run one at a time. Edit-mode tools fail while a playtest is running. Stop play before editing.
- Code run through `execute_luau` gets its own copy of game modules; stubbing a module there does not affect the running game, and "tests" can write to real DataStores. Check and clean your own test entries.
- Large builds: build in chunks, parent small subtrees, `task.wait()` once per chunk, or Studio freezes.
- The Creator Hub UI renders about one step behind: wait ~8 seconds after navigating, focus fields with JS, then type with real key events (values set by JS alone do not enable buttons).
