# Aaron & Chloë Presentation Night

One random topic each, every week. You both open the same link, tap **Spin my topic**, and a
slot machine rattles through topics before landing on yours — on *both* screens at once, so
you can watch each other's land live on the call. Nothing ever repeats, and every week you've
done is kept in the history.

- `index.html` — the whole app
- `topics.js` — the topic list (edit this freely)
- Backend: Firebase Realtime Database (free tier, no card needed)

Setup is about 20 minutes, nearly all of it clicking through Firebase. You only do it once.

---

## Step 1 — Make a Firebase project

1. Go to <https://console.firebase.google.com> and sign in with a Google account.
2. **Create a project**. Name it anything (`presentation-night`).
3. It'll offer Google Analytics — turn it **off**, you don't need it.
4. Wait for it to finish, then **Continue**.

## Step 2 — Create the Realtime Database

⚠️ Firebase has two different databases. You want **Realtime Database**, *not* Firestore.

1. Left sidebar → **Build** → **Realtime Database** → **Create Database**.
2. Pick a location near the two of you (e.g. `europe-west1`).
3. When it asks about security rules, pick **Start in test mode** — you'll set the real
   rules in step 5 either way.

You should now see a database URL like
`https://presentation-night-default-rtdb.europe-west1.firebasedatabase.app/`.

## Step 3 — Register a web app

1. Click the ⚙️ gear (top left) → **Project settings**.
2. Scroll to **Your apps** → click the **`</>`** (web) icon.
3. Nickname it anything. **Do not** tick "Firebase Hosting".
4. Click **Register app**.

Firebase shows you a `firebaseConfig = { ... }` block. Keep that tab open.

## Step 4 — Paste the config in

Open `index.html` **in a code editor** — `cursor index.html`, or VS Code. Don't use TextEdit
or double-click it: both *render* the page instead of showing you the source, which is why the
script looks like it isn't there.

Go to **line 208** (`Cmd+G`), or search for `PASTE_ME`. You're looking for:

```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_ME",
  ...
};
```

Replace it with the object Firebase just gave you. Make sure `databaseURL` is in there —
if it's missing from what Firebase showed you, copy it off the Realtime Database page.

> The API key sitting in the file is fine — a Firebase web key is a public project
> identifier, not a secret. It's in every Firebase web app ever shipped.

## Step 5 — Set the database rules

Realtime Database → **Rules** tab. Replace everything with:

```json
{
  "rules": {
    "rooms": {
      ".read": true,
      ".write": true
    }
  }
}
```

**Publish.** (Firebase will show a warning triangle about the database being open. That's
expected — see the note at the bottom.)

## Step 6 — Put it online

The app uses JavaScript modules, so **double-clicking `index.html` won't work** — browsers
block modules loaded over `file://`. It has to be served over http(s).

**To test on your own machine first:**

```bash
python3 -m http.server 8000 --directory /Users/aaron/Claude/presentation-night
```

Then open <http://localhost:8000>.

**To get a link you can send Chloë**, pick one:

- **Netlify Drop** (easiest, ~30 seconds) — go to <https://app.netlify.com/drop> and drag the
  whole `presentation-night` folder onto the page. You get a live URL straight away. You can
  rename it to something like `aaron-and-chloe.netlify.app` in the site settings afterwards.
- **GitHub Pages** — push the folder to a repo, then Settings → Pages → deploy from `main` / root.
- **Firebase Hosting** — you're already in Firebase: `npm i -g firebase-tools`, then
  `firebase login`, `firebase init hosting` (public directory: `.`), `firebase deploy`.

## Step 7 — First run

1. You both open the link.
2. Tap your own name once — the device remembers it from then on.
3. Hit **Spin my topic**. You'll each see the other's card rattle and land in real time.

---

## Using it

| Button | What it does |
|---|---|
| **Spin my topic** | Picks a topic neither of you has ever had. Locks once you've spun. |
| **Undo my spin** | Puts your topic back and lets you re-roll. Use sparingly or you'll both just fish for the easy ones. |
| **Start next week** | Files both topics into the history, clears the board, moves to the next week. Do this at the *end* of presentation night. |
| **Put every topic back in the pool** | Wipes the used list so everything's available again. For when you've worked through all 170. |
| **switch** (top corner) | Changes which of you this device is. |

Aaron is amber, Chloë is pink, throughout.

## Editing the topics

Open `topics.js` and change whatever you like. Worth knowing:

- **Order doesn't matter.** Topics are tracked by a hash of their text, so you can add,
  delete and reshuffle without breaking the no-repeat history.
- **Rewording a topic** makes it count as a new one, so it becomes available again.
- You both need the *same* `topics.js` — automatic if you're both on the same hosted link.
  Re-upload after every edit, and hard-refresh (Ctrl/Cmd + Shift + R).
- There are about 170 topics in there. At two a week that's a year and a half.

The last section, **Aaron & Chloë specials**, is the personal one — topics about each other,
places you've been, things you want to drag the other person into. Add to it as you go.

## If something breaks

**"The database turned us away"** — the rules aren't published, or `databaseURL` is wrong or
missing from your config. Check the Rules tab shows the JSON from step 5.

**Blank page** — open the browser console (F12). If it mentions modules or CORS, the file is
being opened directly instead of served. See step 6.

**One of you sees a different topic list** — hard refresh. The board is live, but a cached
`topics.js` after an edit can differ between you.

## On the open database

You asked for this and it's a reasonable call, but so it's written down: the rules above let
anyone who knows the database URL read and write your board. Nobody will find it by accident —
there's nothing to search for and nothing to gain — but it isn't locked, and the Firebase
console will keep reminding you of that.

Two things follow from it. Don't put anything private in a topic, and if the reminder ever
starts bothering you, the fix is Firebase Authentication with Google sign-in and rules pinned
to your two accounts. That's about 20 more minutes — ask me and I'll wire it up.
