# Aaron & Chloë Presentation Night

One random topic each, every week. We each get given something to research, then present it to
the other over video call.

**→ [atdenton.github.io/presentation-night](https://atdenton.github.io/presentation-night/)**

## How it works

Open the link, tap your name once, and the device remembers you from then on. Aaron is sky
blue, Chloë is peach — blue's complementary colour — and the two carry through the cards,
the history, and the liquid trail that follows your cursor (violet while nobody's signed in).

Hit **Spin the wheel** and a prize wheel takes over both screens — it rattles up to speed,
clatters to a stop with a tick-tick-tick of the pointer, and lands on your topic with a
confetti pop. Because the board is synced live, the spin plays out on *both* screens at
the same time — so if one of us spins mid-call, the other watches it land in real time
rather than being told about it.

When the night's done, **Start next week** files both topics into the history and clears the
board for the next one. Everything either of us has ever presented is kept in **Memory lane**
below the board — one column each, week by week, forever — and each entry can hold a link to
the actual deck we presented from: hover a week and hit the 📎 to paste a Google Slides /
Drive / anywhere URL. The topic becomes a clickable link. (The site's free hosting can't store
uploaded files, so Memory lane remembers *where the deck lives* rather than the file itself.)

The whole page stretches to fill the screen now: taller cards, a memory section that grows
with the collection, no dead space at the bottom.

| | |
|---|---|
| **Spin the wheel** | Picks a topic neither of us has ever had. Locks once spun. |
| **Undo my spin** | Puts the topic back and re-rolls. For when it's genuinely unworkable, not for fishing. |
| **Start next week** | Archives the week, clears the board, moves the counter on. |
| **Put every topic back in the pool** | Wipes the used list. Only needed once we've been through all 170. |
| **switch** | Changes which of us this device is signed in as. |

## No repeats

Every topic ever handed out is recorded, so nothing comes round twice — and a spin also excludes
whatever the other person is currently holding, so we can't both end up presenting the same thing.

Topics are identified by a hash of their own text rather than their position in the list. That
means the list can be added to, cut down, or reshuffled at any point without corrupting the
history. Rewording a topic does make it count as a new one, so it becomes eligible again.

There are 170 topics in `topics.js`, spread across science, history, unsolved mysteries, art,
psychology, geography, and a section of personal ones about us. At two a week, that's about a
year and a half.

## Under the hood

Three files, no build step, no framework.

- `index.html` — the entire app: markup, styles, and one ES module
- `topics.js` — the topic list
- Firebase Realtime Database for the live sync

Both screens subscribe to one record. Every spin runs as a database transaction, so two
simultaneous spins can't collide or hand out the same topic. The wheel reveal runs on a CSS
transition with a timer fallback, because browsers suspend animation frames in background
tabs — without it, a topic landing while your tab was hidden would freeze mid-spin. The wheel's
segments are plain colour — topics are far too long to fit on a wedge — and the real topic is
revealed when it stops.

The cursor trail is a proper Navier–Stokes fluid simulation running on the GPU (WebGL2):
the pointer injects velocity into a velocity field, which is advected, pressure-projected,
spun up with vorticity confinement, then shaded by its own gradient so the surface catches
light like a real liquid — the aurora background is rendered inside the same shader, so
moving the mouse genuinely parts and drags it like water. The wake is tinted with whatever
the background colour is at each point (and only glows strongly on fast movement), so it
reads as the backdrop moving rather than ink drawn on top. Browsers without WebGL2 fall back to a
lightweight particle stream. All motion respects `prefers-reduced-motion`.

Chosen identity lives in `localStorage`; everything else lives in the database, so the board
looks the same on a laptop, a phone, or a browser you've never opened it in before.
