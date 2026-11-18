# Campus Stores

Campus Stores is a Product Engineer work sample set at a fictional campus media equipment desk. Work through a club's screening, from equipment pickup to a partial return and the next morning's handover. Open **Candidate brief** in the app for the assignment.

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

One idea: the unit of work at this desk is the request ("the Film Society's screening kit"), but the product only thinks in slips (one per unit). Everything I changed pulls the request back into view at the three moments the brief walks through. All changes are in `app.js`.

### What the product handles well

Over-issuing is hard. Trying to issue a second projector against RQ-214 (which approves one), a unit outside the request, or the projector in repair is refused with a clear message. The mistake that is hardest to undo — a wrong item leaving the building — is the one the app blocks best.

### The moment I would change

Friday, 3:45 pm: "Can he leave with everything he needs?" The app can't tell me. What I need to hand over the kit correctly is spread over three screens: the approved items and the note about the USB-C-only laptop are on Requests, who is collecting is in the Desk book, and I issue on the Loan register, whose form shows none of it. I can issue the projector and speaker, hand them over, and never notice the adapter is missing, even though the request note says the screening depends on it.

### What I changed

- **At pickup:** choosing the request in the issue form now shows its kit in place: the note, each approved item, and what is already issued or still to issue. The return time defaults to the request's. After each slip the status line says what is still to issue, or that the kit is complete.
- **At return:** "Record return" asks which unit label is physically in hand. A mismatch names the slip that unit belongs to, or says it isn't out, and closes nothing. This matters because the two adapters (AD-12, AD-15) are identical.
- **At handover:** each request shows what is still out (also summarised on the Requests list and Loan register), and the Desk book lists everything outstanding, built from the register, with notes attachable to a slip.

### The same job with the change

Friday: I pick RQ-214 and see three items and the laptop note. After projector and speaker the status line says the adapter is still to issue, so I issue it and see "kit is complete". 7:30 pm: I take back the projector and speaker; the request reads "1 still out" and the Desk book already tells the morning shift. Saturday: Leela hands over an adapter. I choose AD-12 from its label and the slip closes; choosing AD-15 is refused.

### What I kept, and what I rejected

Kept: one slip per unit, and every existing block. Rejected on purpose:

- **Blocking handover until the kit is complete.** Partial pickups are legitimate here (the Film Society's own return is partial), so I warn instead of block.
- **One-click "issue the whole request".** It skips the per-unit record and needs a quantity model I would be guessing at.
- **Flagging late returns.** Due times are plain text like "Fri 8 pm", so comparing them properly needs a time model first.

### Gaps I noticed but did not fix

- Nothing records who may return the kit: Leela brings back an adapter issued to Kabir, and the app has no way to say that's fine.
- A slip's return time can still be changed away from the request's without a warning.

### One thing to watch at a real counter

Tally, over a week of pickups, how often a kit leaves with an approved item still to issue, and how often a return is corrected afterwards (the wrong slip closed, then reopened). I have no baseline, so the first week would only set one; the checklist helped if both tallies fall in the weeks after.

### How I worked

I used Claude to help write and test this. I worked through the shift myself first, and the observations above are mine.
