# Deadlock Threat Icons: settings store

This one-page site keeps the in-game settings of the Deadlock Threat Icons mod between game launches.

Deadlock gives mods no way to write files. So the mod opens this page, once per game launch, in a hidden browser panel inside the game, and the page keeps the settings in that browser's storage, on the player's own PC.

- Requests travel in the part of the address after `#` (`index.html#{"o":"w",...}`). Browsers never send that part to a server, so GitHub only sees an ordinary page visit.
- The page answers by changing its title to `TMS1:` followed by a small JSON text.
- It reads and writes one storage key only: `deadlock-threat-icons/settings`.
- The saved text holds only the settings that differ from the mod's defaults, for example `{"m":"TMSET","v":1,"s":{"USE_ARROWS":0}}`.

The same technique is used by HP Colors v2. Each mod has its own page, so their saves never touch each other.

## Protocol (TMS1, version 1)

| Request (after `#`, URL-encoded JSON) | Answer (page title, after `TMS1:`) |
| --- | --- |
| `{"o":"hello","i":"h1"}` | `{"o":"ready","i":"h1","ok":true,"v":1,"h":"<page address>"}` |
| `{"o":"r","i":"q2"}` | `{"o":"r","i":"q2","ok":true,"x":0}` (nothing saved) or `"x":1,"d":"<saved text>"` |
| `{"o":"w","i":"q3","v":"<text>"}` | `{"o":"w","i":"q3","ok":true}` |
| `{"o":"d","i":"q4"}` | `{"o":"d","i":"q4","ok":true}` |

A request that fails gets `"ok":false` and a short reason in `"e"`. Values are limited to 3000 characters.

If the request or answer format ever changes, `VERSION` in `index.html` and `STORE_PAGE_VERSION` in the mod change together, and an old mod refuses a new page (and the other way round) instead of guessing.
