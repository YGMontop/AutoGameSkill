# Making and uploading images

## Image list (from the design doc)
| Use | Size | Notes |
|---|---|---|
| Game icon | 512x512 | Bold subject, readable at 150px, no tiny text |
| Thumbnails | 1920x1080 (16:9), 3 of them | Different scenes: action, map, items |
| Gamepass icons | 512x512 | One clear symbol per pass, name not required |
| Developer product icons | 512x512 | Same style family as passes |
| UI art | varies, power-of-two friendly | Buttons, frames, currency icons, logos |
| Decals/textures | 512 or 1024 square | Tileable if used as repeating textures |

All art follows the approved palette and style so everything looks like one game.

## Three ways to make them
1. **Code-drawn (icons, thumbnails, UI, logos):**
   - Design in HTML/CSS/SVG. Render to PNG with headless Edge at an exact size:
     `msedge --headless --disable-gpu --hide-scrollbars --screenshot="<out.png>" --window-size=1920,1080 "file:///<path>/design.html"`
     (find Edge under `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`).
   - Or draw with Python Pillow (`py -m pip install pillow` if missing).
   - Check each PNG with the Read tool (it shows images) before uploading. Fix clipping, unreadable text, off-palette colors.
2. **Studio AI for in-world art:**
   - `generate_texture` re-textures an existing MeshPart or Model from a prompt (use "Fast" first, "Quality" if needed).
   - `generate_material` makes a MaterialVariant for terrain and parts (needs baseMaterial, description, id, pattern).
   - `generate_mesh` / `generate_procedural_model` are covered in `models-and-map.md`.
3. **Reference images:** `store_image` turns a local PNG/JPG (max 5 MB) into an `IMAGEID_...` URI for `generate_procedural_model` or `generate_scene` as a style reference.

## Upload
- `upload_image` takes URLs, not local paths. Serve the folder: `py -m http.server 8765 --directory assets/images` (run in the background), then pass `http://localhost:8765/<file>.png`. It returns `rbxassetid://<id>`.
- Uploads are moderated and can be rejected or delayed. After upload, check the asset loads: put it in a test `ImageLabel` / `Decal` in Edit mode and `ContentProvider:PreloadAsync`, and look at it with `screen_capture`.
- Image id vs decal id: a Decal-type upload can differ from the Image id. Use whichever loads in an `ImageLabel.Image` and note which one in the registry.
- Record every id under `GameAssets.Images` with a comment of what it is. Game code reads ids from `GameAssets`, never inline.
- Gamepass/product/game icons are uploaded through Creator Hub (see `creator-hub.md`), not `upload_image`.
- Stop the local server when done.

## Quality bar
No placeholder art, no lorem text, no copyrighted characters or logos, no real brands, no text that is cut off. Icons must be recognizable at small size.
