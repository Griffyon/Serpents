# TornTargets – user guide

TornTargets is a single web page (`TornTargets/index.html`) that lists the members of a Torn faction with their Fair Fight (FF) value, activity and hospital time. It runs entirely in your browser and has no backend.

## What you need
- A Torn API key. A public-level key is enough; a limited or full key also works.
- An FFScouter API key.
- Both keys are 16 letters/digits.

## Open the page
Open `TornTargets/index.html` in a modern browser (double-click, or serve the folder with any static web server).

## Log in
1. Enter your Torn API key and your FFScouter API key.
2. Click **Log in**. Both keys are checked against their services.
3. Until you are logged in, everything below the login box is disabled.
4. Once logged in, the login box disappears and only a **Log out** button remains, top right next to the title. Click it to lock the page and show the login box again.

Storage (browser local storage):
- Your keys are saved after a successful login, so the fields are filled in next time. You still click **Log in** each visit.
- The last selected faction (name and ID) is saved. After logging in, that faction opens automatically.
- **Log out** locks the page but does not delete the saved keys or faction. To wipe them, clear this site's data in your browser.
- Keys are sent only to `api.torn.com` and `ffscouter.com`.

## Choose a faction
- Type a faction name and click **Search** (or press Enter), then click a result.
- Or type a numeric faction ID to open that faction directly. A faction whose name is only digits cannot be found by name.

## Reading the table
| Column | Meaning |
|---|---|
| User / ID / Lvl | Member name (links to the Torn profile), user ID and level. |
| Status | A ball: green = online, orange = idle, gray = offline. Hover an idle or offline ball to see when the last action was (relative time plus the exact local date/time). Sorting puts online first, then idle, then offline. The column header shows `(online/idle)` counts for the whole faction (ignoring filters), updated whenever the numbers change. |
| FF | Fair Fight from FFScouter. `?` means FFScouter has no data for that player. |
| Est. stats | FFScouter's battle-stat estimate. |
| State | Torn state: Okay, Hospital, Traveling, Abroad, Jail, and so on. |
| Hospital | Time left in hospital as `hh:mm:ss`, counting down every second. Blank if not in hospital. When the countdown reaches zero the player is treated as out of hospital straight away (state Okay, no time), so when sorted on Hospital the player drops out of the hospital group at once. Under 30 seconds the cell turns solid red with bold white text, so a player about to leave hospital stands out. |
| Attack | ⚔️ opens the Torn attack page for that player in a new tab. Next to it, 📋 copies a one-line summary to the clipboard (a ✓ appears briefly): `<username> - <attack link> - <other info>`, where the other info is `[hh:mm:ss] [ONLINE] [FF x.xx]`. If there is no other info, the last ` - ` part is left out. The time is the remaining hospital time and is left out when the player is not in hospital; `ONLINE` is added only when the player is online; the FF part is left out when FF is unknown. Example: `Bob - https://www.torn.com/page.php?sid=attack&user2ID=123 - 00:12:34 ONLINE FF 3.45`. Hover 📋 to preview the text. |

Click any column header (all except Attack) to sort A–Z / lowest first; click again to reverse (Z–A / highest first). The active column shows ▲ for ascending or ▼ for descending. Unknown values always sort last. The default sort is FF, from the highest value to the lowest.

## Filters
Filters are hidden by default. Click **Show filters** above the table to open them and **Hide filters** to close them. Whether they are open is remembered in this browser. Filters you have set stay applied while hidden; the button then reads "Show filters (active)".

In the filter bar:
- **Status:** tick online, idle and/or offline to show only those. Nothing ticked shows everyone.
- **FF between … and …:** show only players whose FF is in the range (both ends included). Fill in just one box for a minimum or maximum only. Empty boxes mean no FF filter.
- When an FF range is set, players with unknown FF (`?`) are hidden.
- **Clear** resets all filters. The "x of y shown" counter shows how many players match. Filters are not saved between visits.

## Refresh
- Members refresh every 10 seconds by default. Change this with the **Refresh** dropdown (10, 15, 30, 60 s or manual), or click **Refresh now**.
- FF values are re-fetched about once a minute, because FFScouter caches data for up to 5 minutes.
- Request load is about 6 Torn calls per minute plus about 1 FFScouter call per minute (205 members per call), well inside both services' limits.

## Troubleshooting
- "Incorrect key" – check the Torn key. An FFScouter error means the FFScouter key was rejected.
- FFScouter rate limit (HTTP 429) shows a retry time in the status line.
- FF shows `?` for everyone – the FFScouter key may lack data; check it on ffscouter.com.

## Mobile
The page is responsive. On screens up to 640 px wide:
- The key fields, search box and buttons stack to full width and get larger touch targets.
- Each player is shown as a card with two lines: status ball, username and FF on the first line; hospital time (or the state, if not Okay) with the ⚔️/📋 buttons on the second line. The ID, Lvl, Est. stats and State columns are hidden.
- The column headers become a row of sort links (User, Status, FF, Hospital) above the cards.
- There is never a horizontal scrollbar.

On wider screens the full table with all columns is shown; if it is too wide for your screen it scrolls sideways.

## verify.html (Discord bot registration)
`verify.html` is the page the Discord bot's `/verify` command links to (`verify.html?code=<one-time code>`).
- Two fields: **Torn API key** and **FFScouter API key**. Both "Get key" buttons open `https://www.torn.com/preferences.php#tab=api`.
- **Validate and register** checks both keys in the browser (Torn `GET /user/basic`, then FFScouter `get-stats` for your player ID). Each field shows a ✓ or the error.
- **Fail hard:** if either key fails validation, the page shows the error, says "Registration failed. Nothing was saved." and sends nothing. An FFScouter failure tells the user to activate FFScouter manually following the FFScouter instructions and try again.
- Only when both are valid they are POSTed as JSON `{ code, tornKey, ffScouterKey }` to the address in the `VERIFY_API` constant at the top of the script. **Temporarily this is `http://localhost:5196/verify`**, the TornAPI running on the same computer as the browser, so registration only works for someone running TornAPI locally. Replace it with the public HTTPS address of the server before real use.
- **Local testing:** run TornAPI locally (`dotnet run`, port 5196) with `Verify:AllowedOrigin` set to the origin that serves this page (`https://griffyon.github.io`), then open the link from `/verify`. Browsers allow an HTTPS page to call `http://localhost`; Chrome may ask for permission to access the local network.
- On success the fields are cleared, the code is removed from the address bar and the page shows "You have completed registration and you may now close this page."
- Without a `code` in the address the page redirects to `index.html` (relative, so it also works under the `/Serpents/` path on GitHub Pages).
- Not verified: the exact FFScouter success/error shapes and the server endpoint (not built yet).
