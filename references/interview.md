# Interview guide

Purpose: know exactly what the user wants before building. Ask a lot, but never ask what the idea already answers.

## How to run it
- Use `AskUserQuestion`, max 4 questions per call, 2-4 options each, recommended option first with "(Recommended)". The user can always pick "Other" or say "you decide".
- **Adaptive**: start with the first round (about 10 questions over 2-3 calls). Then keep a list of open gaps against the design doc fields and ask until none remain. Stop when you could write the design doc without guessing.
- Echo back what you understood from the idea before the first round, in two lines, so wrong readings get caught early.
- Never ask the user to type ids, passwords or keys.
- Questions about taste (art style, tone, names) get concrete options, not open prompts.

## Round 1: the big picture
1. Genre and reference: tycoon, obby, simulator, tower defense, fighting, horror, roleplay, survival, racing, party, or a mix. Name a game it should feel like.
2. Player count and mode: solo-friendly, 1-6 co-op, 10-30 per server, PvP, or competitive rounds.
3. Core loop in one sentence: what does the player do every 30 seconds, every 5 minutes, every session?
4. Win/lose and progression: endless upgrades, levels, rebirth/prestige, rounds with winners, story chapters.
5. Session length target and "one more turn" hook.
6. Platforms: PC, mobile, console, VR. Mobile-first changes UI and controls.
7. Art style: low-poly, cartoon, realistic, blocky, pastel, neon, dark. Color mood.
8. Tone and audience: kids, teens, all ages. Scary level, violence level (affects the maturity questionnaire).
9. Game name: a name they already have, or pick from 4 you propose.
10. Theme/setting: where and when does it happen.

## Round 2: depth (ask only what applies)
- **Map**: size, number of zones, hub or open world, vertical or flat, landmarks, day/night, weather, terrain vs built parts.
- **Gameplay systems**: currencies, items, upgrades, pets, weapons, vehicles, crafting, quests, NPCs, enemies, bosses, minigames, events.
- **Social**: trading, friends perks, parties, leaderboards, chat features, emotes, spectating.
- **Controls and camera**: default, shift-lock, top-down, first-person, vehicle controls, mobile buttons.
- **UI**: HUD content, menus needed, shop style, tutorial style, settings (volume, graphics, mute music).
- **Audio**: music mood, SFX style, ambient sound. (Use only sounds verified to load.)
- **Saving**: what must persist, rebirth/reset rules, offline progress.
- **Onboarding**: tutorial, first 2 minutes, starter rewards, daily rewards, badges.
- **Difficulty and balance**: casual vs hard, scaling curves, anti-grind.
- **Moderation and safety**: chat, user-generated content, report flow, anti-exploit concerns.
- **Scope**: "small and polished" vs "big and feature-rich". Content amount at launch (levels, items, zones).

## Round 3: monetization (the user decides; always ask)
- Do they want monetization at all? (If none, skip passes and products entirely.)
- Which kinds: gamepasses (permanent perks), developer products (consumables, currency packs, revives, skips), cosmetics, VIP tier, private servers, subscriptions, limited-time items.
- How many, and price level (low R$25-99, mid R$100-499, high R$500+).
- What must stay free for everyone (core progression, fairness line).
- Any paid random items? If yes, odds will be shown to players.
- You then propose a concrete price list in the design doc: name, price, exact behavior, icon idea. They approve it as part of the single gate.

## Round 4: publishing
- Experience owner: user or group (ask the user to name the group; they sign in themselves).
- Game title and description tone, genre tag, who can play (public, friends, private first).
- Anything they want in the thumbnails (characters, logo text, color).
- Existing place content: build beside it, replace it, or new place.

## Gap checklist (stop asking when all are filled)
name, genre, loop, progression, win/lose, players, platforms, art style, tone, map zones and layout, systems list, UI list, audio mood, saved data, monetization decision and price list, onboarding, asset owner, publish settings.
