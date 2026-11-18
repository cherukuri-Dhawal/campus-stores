# Campus Stores

Campus Stores is a Product Engineer work sample set at a fictional campus media equipment desk. Work through a club’s screening, from equipment pickup to a partial return and the next morning’s handover. Open **Candidate brief** in the app for the assignment.

## Run the app

Open `index.html` in a modern browser. The app includes its scripts, styles, and icons and can run offline.

To serve it over local HTTP, run this from the repository folder and visit `http://localhost:8000`.

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Each browser stores its own sample shift locally when browser storage is available. **Restart sample shift** restores the fictional starting records.

## Source

- `index.html` contains the desk layout, candidate brief, and bundled icons.
- `app.js` contains the sample records and interactions.
- `styles.css` defines the desktop and mobile layouts.

Icon license notices are in [third-party/lucide-LICENSE.txt](third-party/lucide-LICENSE.txt).


## Submit your work

1. [Fork Campus Stores](https://github.com/virinchi-healthcare/campus-stores/fork) into your GitHub account. Complete the candidate brief and push your changes to your public fork. In your README, point reviewers to your work and explain the decision you made.
2. In your fork, open **Commits**, select the final commit you want reviewed, and copy the page URL. It should look like `https://github.com/YOUR-USERNAME/campus-stores/commit/COMMIT-SHA`.
3. [Submit your work through the Google Form](https://docs.google.com/forms/d/e/1FAIpQLScvzoh1R4WS-SkiwAVBQbMvniieJYSoOEfLMyVo5CyjOvYUgQ/viewform). Your **roll number**, **email ID**, and **final commit link from your public fork** are required. Enter your roll number and email ID in the form only.

---

## My submission — for reviewers

**The unit of work is the request, not the slip.** Every question at the counter — "did we give them everything?", "what's still out?", "which adapter is theirs?" — is a question about a request. The original product tracks individual slips well but never surfaces the request's status as a whole.

### Where to look

All changes are in `app.js`. Open `index.html` directly in a browser or serve it with `python3 -m http.server 8000 --bind 127.0.0.1`.

### What the product handles well

When I tried to issue PR-04 (HDMI projector) under RQ-209 (Debate Union, which only has "Wireless microphone" approved), the app immediately blocked me with *"This equipment type is outside the approved request."* No slip was created. That's exactly right: a desk worker under pressure can't memorise every form, and the app enforces the boundary without any extra step. The same guard fires for over-quantity and for units currently in repair.

### The one thing I'd change — and why

On Saturday morning I clicked **Record return** on L-404 (the Film Society's adapter, which could be AD-12 or AD-15 — two physically identical units in the same bin). The original app closed the slip immediately, with no check on which unit was physically in my hand. If I had picked the wrong adapter from the pile, the wrong slip would close, leaving the other adapter still marked as on loan with no way to know it was actually back. The root problem: return confirmation was keyed to the slip number, not to the physical unit label.

### What I changed (three additions to `app.js`)

- **Request-level checklist.** Opening a request now shows each approved item with its status — *Still out*, *Returned*, or *No slip yet* — plus the unit ID, slip number, and person. The request list and loan register rows show a summary (e.g. *RQ-214 · 1 still out*) at a glance.
- **Unit-confirmed returns.** Clicking *Record return* now shows a dropdown: "Which label is physically in your hand?" If the label matches the slip, it closes. If it matches a *different* open slip, the app names that slip and closes nothing. If it isn't on loan at all, the app says so.
- **Auto-built handover.** The Desk book's *Outstanding right now* section is generated live from the loan register — not typed by hand. Handover notes can optionally be attached to a specific slip so the morning shift knows exactly which loan is still open.

### The same job, walked through with the change

**Friday 3:45 pm — pickup.** I open RQ-214 and see the checklist: *No slip yet* against all three items. I issue PR-04, SP-02, and AD-12 to Kabir. The checklist updates to *Still out* for each. The request list row reads *RQ-214 · 3 still out*.

**Friday 7:30 pm — partial return.** Kabir brings back the projector and speaker. I click *Record return* on L-402 (projector). The confirmation dropdown appears; I select PR-04 — it matches, slip closes. Same for L-403 (SP-02). The checklist now shows *Returned* for two items and *Still out* for the adapter. I go to the Desk book: *Outstanding right now* lists L-404 (AD-12 · RQ-214 · Kabir Sen · Due Fri 8 pm) automatically. I add a handover note attached to L-404: *"Leela Das will return Saturday 9 am."*

**Saturday 9 am — handover.** I read the Desk book and see the outstanding loan and the attached note in one place. Leela walks in. I click *Record return* on L-404. The dropdown shows. I select AD-15 (the wrong adapter). The app says *"AD-15 is not currently on loan. Slip L-404 not closed."* I look again, pick AD-12, confirm — slip closes. Checklist shows *All returned*.

### What I kept

- All the original guards: wrong equipment type, over-quantity, units in repair.
- The existing data model: one slip per physical unit.
- `localStorage` restore works with old saved data; `returning: null` defaults safely.

### Alternatives I rejected

- **Issue the whole kit in one click.** Attractive, but the original model issues one slip per unit on purpose. A one-click issue would need a real quantity model (the app has none) and would skip the check for which physical unit is leaving. Scope too large; left out.
- **Flag when slip due time doesn't match request due time.** RQ-214 says *Fri 8 pm*, but the issue form lets me pick *Sat 9 am* with no warning. Worth fixing, but it needs a due-time parser to compare strings reliably. Left out on purpose to keep scope tight.
- **Validate that Leela is authorised to return Kabir's item.** The request doesn't name Leela at all. A real fix would require linking authorised returners to a request. Deferred.

### Known gaps I noticed but did not fix

- Nothing records that Leela is allowed to return Kabir's adapter. The app will accept a return from anyone.
- Slip due time is not tied to the request's due time — a desk worker can issue under RQ-214 with *Sat 9 am* and nothing flags the mismatch.
- The request checklist matches by equipment *type*, not by the specific unit. If two slips of the same type existed on one request, the checklist would only find the first. The business rule (one unit per type per request) prevents this today, but it's a hidden assumption.

### One measurable observation at a real counter

**What to count:** returns that need a correction after the fact — a slip closed on the wrong unit and then manually fixed by a supervisor, or a unit marked "on loan" at end-of-day that is physically on the shelf.

**How to count it:** review the register every Friday evening and Saturday morning for one month. Count slips that were reopened or notes that say "wrong unit closed."

**My assumptions going in (untested — to be checked against real data):** in a desk handling 20–30 weekend loans, I'd guess roughly 1–2 wrong-unit closures per week when two identical adapters are in circulation. After the unit-confirmation step I'd guess that drops to zero. These numbers are my own assumptions; a real counter could easily show something different. If the number doesn't drop, the confirmation step adds friction without fixing the root cause (and the units probably need clearer physical labelling).

### AI use

I used Claude to help build and test this; I ran the shift myself and the observations are mine.
