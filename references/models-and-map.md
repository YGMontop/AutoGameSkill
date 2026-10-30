# 3D models and the map

## Sizing
The avatar is about 2.4 x 6.6 x 2.4 studs. Doors at least 8 studs tall and 5 wide, ceilings at least 12, stairs 1 stud rise, jump height ~7.2 studs, walk speed 16. Size everything against that. The front of a build faces -Z.

## Choosing a method per model
| Need | Method |
|---|---|
| Hero props, creatures, weapons, unique items | `generate_mesh` (textured AI mesh) |
| Buildings, trees, rocks, obstacles, vehicles with tweakable attributes | `generate_procedural_model` |
| Floors, walls, platforms, simple shapes | Parts, wedges, unions, built via `execute_luau` |
| Common props (benches, lamps, crates) when AI would be overkill | Toolbox via `search_asset` + scanned `insert_asset` |
| Whole themed areas as a starting point | `generate_scene` inside a bounding box, then clean up |

### Generating
- `generate_mesh`: give `textPrompt`, `size`, `maxTriangles` (12-20000; use 3000-8000 for props, up to 15000 for heroes), and `segmentation` (`none` for one piece, `explicit` + `partNames` for moving parts). Start several with `async: true`, then `wait_job_finished` for each when needed.
- `generate_procedural_model`: pass the user's own words plus the attributes to expose. Async too.
- Inspect each result: `screen_capture` plus `inspect_instance`. Reject meshes with holes, wrong scale, or off-style look and regenerate with a better prompt.
- Set generated parts `Anchored` unless they must move, give `CanCollide`/`CanTouch`/`CanQuery` deliberately, set `CollisionFidelity` sensibly (`Box` or `Hull` for decor).
- Group in Models with meaningful names, set `PrimaryPart`, put reusable ones in `ServerStorage.Assets` or `ReplicatedStorage.Assets`, then clone into the map.

## Toolbox safety (mandatory scan)
1. `search_asset` with `scope: creator_store`, prefer `verifiedCreatorsOnly`, `priceFilter: free`. Look at thumbnails.
2. `insert_asset` into a quarantine folder (`ServerStorage.Quarantine`), never straight into the map.
3. Scan with `execute_luau`: list every descendant that is a `Script`, `LocalScript`, `ModuleScript`, `RemoteEvent`, `RemoteFunction`, `BindableEvent`, `Sound`, `ParticleEmitter`, or `Decal/Texture` pointing at an asset id; grep script sources for `require(`, `getfenv`, `setfenv`, `loadstring`, `HttpService`, `InsertService`, `MarketplaceService`, `TeleportService`, `Players`, long numeric ids, or obfuscated strings.
4. Delete all scripts and remotes. Keep only geometry, materials and verified decals/sounds. If anything looked obfuscated or malicious, drop the asset entirely.
5. If the asset's creator differs from the place owner, name the source and destination and ask the user before using it (cross-owner caution from `search_asset`).
6. Move survivors into `Assets`, rename them, note creator and id in the progress file.

## Map building
1. Turn the design-doc layout into coordinates: origin at the spawn, zones along clear axes, paths between them.
2. Terrain: `Terrain:FillBlock` / `FillBall` / `FillCylinder` and `generate_material` variants for biomes; use `execute_luau` with a deterministic seed and chunking.
3. Structure: build zones in separate Folders (`Map/Zone_Name`). One chunk per `execute_luau`, `task.wait()` between chunks, parent small subtrees.
4. Detail pass: props, signs, lighting, particles, sounds, decals. Keep part count sane (aim under about 10k parts per zone; use meshes and unions for detail).
5. Lighting: set `Lighting` (ClockTime, Brightness, Ambient, OutdoorAmbient), `Atmosphere`, `Sky`, `ColorCorrection`, `Bloom` sparingly, to match the palette.
6. Gameplay anchors: `SpawnLocation`(s) with a safe spawn, checkpoints, shop NPC spots, interaction points with `ProximityPrompt`, `CollectionService` tags for things code drives (tags are named in the design doc).
7. Safety: kill-floor under the map, invisible walls at the edge, no gaps a player can fall through, no unreachable required areas. Check with a walk-through using `character_navigation`.
8. Set `StreamingEnabled` for big maps and mark important models `Persistent` if needed.
9. After each zone: `screen_capture` from a few angles, check scale against an avatar, fix.

## Output check
Every zone captured and reviewed, spawn works, all tags exist, nothing is floating or unanchored by accident, no leftover Quarantine folder.
