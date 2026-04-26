# Circle Sender (mobile web app)

This project is a tiny mobile web app that lets someone **hold + drag to draw a circle**, then enter a name, then **send a message over WebSocket** and view a simple history list.

It’s just **one HTML file** plus this README.

## Files

- `index.html` — the entire app (screens + drawing + WebSocket + history)
- `README.md` — this guide

## How to run it

1. Open `index.html` in a browser (mobile Safari/Chrome works best).
2. Make sure the computer/device can reach your WebSocket server at port **9980**.

If you don’t have a server running yet, the app will still work visually; it will just keep trying to reconnect.

---

## Change the WebSocket IP and port

Open `index.html` and find the “Config you’ll likely edit” section near the top of the `<script>`.

Change these variables:

- **`WS_HOST`** (default is `"[HOST_IP]"`)
  - If you leave it as `"[HOST_IP]"`, the app will automatically use the same host as the page (your browser’s current hostname).
  - To force an IP, set it like: `const WS_HOST = "192.168.1.10";`
- **`WS_PORT`** (default `9980`)

They are on these lines in `index.html`:

- Line **258**: `const WS_HOST = "[HOST_IP]";`
- Line **259**: `const WS_PORT = 9980;`

---

## Change the JSON data format being sent

The message is built in the function **`buildPayload(name)`**.

Right now it sends:

```json
{ "type": "circle", "name": "wen", "size": 1.34, "speed": 520 }
```

To change the shape of that JSON, edit:

- **`PAYLOAD_TYPE`** (sets `"type"`)
- the returned object inside **`buildPayload(name)`**

Tip: the actual sending happens in **`sendPayload(payload)`** which does `JSON.stringify(payload)`.

Exact places in `index.html`:

- Line **260**: `const PAYLOAD_TYPE = "circle";`
- Lines **387–394**: `function buildPayload(name) { ... }`
- Lines **475–482**: `function sendPayload(payload) { ... }`

---

## Adjust the circle’s visual style (size, color, stroke width)

All drawing is done with SVG (inside `#drawLayer`).

### Stroke width

Change the CSS variable near the top of `index.html`:

- **`--stroke`** (default `2`) — Line **17**

### Line style (dotted guide line)

Change:

- **`--dash`** (default `3 7`) — makes the dotted pattern tighter/looser — Line **18**

### Color

Most “white” drawing uses `#fff`.

Search in `index.html` for `stroke="#fff"` (for circles/lines) and change it.

### Minimum circle size

If you want a minimum visible circle even for tiny drags, change:

- **`--min-radius-px`** (default `6`) — Line **16**

---

## Adjust transition timings (2-second delay, fade duration)

### The 2-second wait after releasing

Change the CSS variable:

- **`--delay-to-name-ms`** (default `2000`) — Line **15**

### The fade-out duration

Change the JS constant:

- **`FADE_DURATION_MS`** (default `500`) — Line **265**

Also, the fade uses the CSS variable:

- **`--fade`** (default `0.5s`)

If you change one, you usually want the other to match.

---

## Modify the name input screen text

In `index.html`, find:

- **`<div class="prompt" id="namePrompt">what's your name?</div>`**

Edit that text to whatever you want.

---

## Reset or clear the history table

History is kept only in memory (it resets if you refresh the page).

### Clear history when pressing “again?”

Find the click handler for **`againBtn`**:

- `againBtn.addEventListener("click", () => { ... })`

Inside it, add these two lines:

- `history.length = 0;`
- `renderHistory();`

### Clear history at any time

You can also clear it anywhere by doing the same two lines:

- `history.length = 0;`
- `renderHistory();`

---

## Notes about `size` and `speed`

- **`size`** is the final radius, normalized to screen width, mapped to **0.0–2.0**:
  - `size = (radiusPx / window.innerWidth) * 2` (then clamped)
- **`speed`** is the average drag speed in pixels/second:
  - total path distance ÷ total drag time

