# Creator Hub work in the integrated browser

Tools: `mcp__Claude_Browser__*` (`preview_start` with a `url`, `navigate`, `read_page`, `find`, `computer`, `javascript_tool`, `get_page_text`). Prefer `read_page`/`get_page_text` over screenshots.

## Sign-in (user does it, you wait)
1. `preview_start` with `url: https://create.roblox.com/dashboard/creations`.
2. If a sign-in page shows, tell the user: "Please sign in in the browser pane. I won't see or type your password. Tell me when you're done." Then stop and wait.
3. Never fill the username, password, 2FA or captcha. Never complete captchas.
4. When they say they're in, read the page and confirm you can see their experiences. If the asset owner is a group, switch to it in the owner selector.
5. If a site-permission prompt appears for the browser pane, tell the user and wait.

## UI behavior that bites
- The Creator Hub UI renders about one step behind. After navigating, wait ~8 seconds (`computer` wait) before reading.
- Fields: focus with JS (`element.focus()`), then type with real key events (`computer` type). Values set only by JS do not enable the Save/Create buttons.
- Terms or consent dialogs (for example Managed Pricing): do not accept on the user's behalf. Prefer the path that avoids them (leave Managed Pricing off), or ask the user.
- URL shapes change. If a path 404s, navigate from the dashboard: Creations -> the experience -> Monetization -> Passes / Developer Products. Common shapes: `.../dashboard/creations/experiences/<universeId>/passes` and `.../developer-products`.

## Find ids
- Universe id: from the experience URL, or in Studio `game.GameId`.
- Place id: `game.PlaceId` (the place must be published first; if the place is unpublished, ask the user before publishing it, because passes need an experience).
- Pass or product ids: from the page URL or the item's detail page after creation. Re-read the list afterwards and match names to prices to be sure.

## Create a gamepass
Name, description (exact behavior), icon (512x512 PNG, required), price (R$). Leave it on-sale only if the design doc says so. After creation, read back the list and copy the id.

## Create a developer product
Name, description, icon (512x512, optional but do it), price. Record the id.

## Uploading an icon file with no file picker
The built-in browser has no file-upload tool. Technique:
1. Serve the image folder over HTTP with CORS enabled (a small Python script that adds `Access-Control-Allow-Origin: *`, running in the background).
2. In `javascript_tool`: `fetch` the image, build a `File`, put it in a `DataTransfer`, assign `input.files = dt.files` on the page's `<input type=file>`, and dispatch `change` and `input` events.
3. Confirm the preview appears before saving.
4. Fallback: if the page rejects it, ask the user to drag the PNG from `assets/images/` into the page, or use Claude in Chrome (`mcp__claude-in-chrome__file_upload`) if they ask for it.
5. Stop the local server when done.

## Game page and publishing
- Studio side: the place must be published before Creator Hub can edit its page. Publishing from Studio: use File -> Publish to Roblox through `user_keyboard_input`/`user_mouse_input` with `screen_capture` to check, after the user confirms publishing.
- Creator Hub: title, description, genre, device support, icon, thumbnails (16:9, up to 3-5), social links off unless the user wants them.
- The maturity questionnaire: ask the user for each answer. Do not guess.
- Public vs private: ask. Default recommendation is to publish private first, test the live version, then make public on their say-so.

## Cleanup
At the end, offer to sign out of Creator Hub, close the browser tabs, stop any local server, and delete scratch files. Do it only if they say yes.
