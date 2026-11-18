# Campus Stores: my submission

Campus Stores is a Product Engineer work sample set at a fictional campus media equipment desk. This fork contains my answer to the candidate brief: **would I keep this product, and what would I change?**

**Short answer:** I would keep it. The data model is right, since each physical unit gets its own slip. The weak point is the moment a kit stops travelling together. When the adapter stayed with Leela overnight, the only place that fact could live was a free-text note, and the slip itself kept saying the wrong thing. My change writes that fact onto the slip, and then makes sure the morning clerk sees it first.

- **Try my version:** open `index.html` (extract the whole folder first, the page needs its CSS and JS beside it)
- **Compare with the original:** open `original/index.html` (the untouched starting files)

Inside the app, **What changed** in the top strip gives the same summary.

![Saturday morning in my version: the slip says Leela has the adapter](docs/3-saturday-today.png)

## Run the app

Open `index.html` in a modern browser. It needs no build step, has no dependencies, and runs offline.

To serve it over local HTTP:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Then visit `http://localhost:8000` (or `/original/` for the starting version). **Restart sample shift** resets the fictional records.

---

## 1. A moment the product handles well

**Friday 7:30 pm: recording a partial return.** Kabir brought back the projector and speaker but not the adapter. In the original app I clicked **Record return** on two slips and left the third open. Inventory immediately showed PR-04 and SP-02 back on the shelf, while AD-12 stayed on loan.

That only works because the original author made **one slip per physical unit** instead of one record per request. With a single "kit" record I would have had to either close the whole kit (losing track of the adapter) or keep it all open (hiding a projector that was actually on the shelf).

The issue form's guardrails are also good. When I tried to put a speaker on the Photo Circle's request, it was refused, and PR-06 (in repair) never appears as an option.

## 2. A moment I would change

**Saturday 9:00 am: Leela walks in with an adapter.** As the morning clerk in the original app, I opened the Loan register and saw:

> L-404 · AD-12 · RQ-214 · Kabir Sen · Due Fri 8 pm

Nothing on that row mentioned Leela, showed that the item was a USB-C adapter, or said that a 9 am return had been agreed. To connect "Leela + adapter" to "L-404 under Kabir's name", I had to open the Desk book and read the free-text note the evening shift left. I then had to trust it referred to AD-12 and not AD-15, the other adapter that sits in the same bin, C4.

The root cause shows up the night before. At 7:30 pm the app let me close two slips without asking what happened to the third. The handover went into a separate, unlinked note, so the slip went on stating something no longer true: "Kabir has it, due 8 pm."

## 3. My change: the slip records who really has it

The core decision: **when custody changes, record it on the slip and copy it to the desk book**, rather than improving note-taking. The register is where the morning clerk looks when someone walks up holding equipment, so that is where the truth has to be. The desk book stays the log of what happened, but it is no longer the only place a fact lives.

### Walking through the same job with my version

| Time | What I do | What I see |
|---|---|---|
| Fri 3:45 pm | **Today** lists RQ-214 under *Ready to issue*, with "collector Kabir Sen, named by Isha Rao". I click **Issue kit**. | Isha's desk book note is shown on the request. Kabir and Fri 8 pm are pre-filled, and each unit is listed with its bin (`AD-12 · Belkin AVC002 · Bin C4`). One click writes L-402, L-403 and L-404, still one slip per unit. |
| Fri 7:30 pm | **Today** shows RQ-214 under *Coming back soon*. I click **Check in kit**. | Every open slip asks *Back at the counter* or *Still out*, and I can't save until each is answered. For L-404 I pick Still out, set **Leela Das** and **Sat 9 am**, and add a note. The handover is written on the slip and logged in the desk book with the slip number. |
| Sat 9:00 am | **Today** opens with *Expected at the counter*: AD-12, **Held by Leela Das · expected Sat 9 am**. I click **Record return**. | The return form asks me to tick that the tag reads AD-12 and warns that AD-15 is the same type. The slip closes, history shows all three events, and the toast tells me to shelve it in Bin C4. |

Screenshots: [Friday Today](docs/0-friday-today.png) · [Issue kit](docs/1-friday-issue-kit.png) · [Friday check-in](docs/2-friday-check-in.png) · [Saturday Today](docs/3-saturday-today.png) · [Saturday close](docs/4-saturday-close-slip.png) · [Desk assistant](docs/5-assistant.png) · [Command menu](docs/6-command-menu.png) · [Phone](docs/7-phone.png)

### Supporting decisions, each tied to a moment in the shift

- **A Today screen ordered by urgency** (overdue → expected at the counter → coming back soon → ready to issue). A clerk taking over a shift shouldn't have to read four tabs to learn what's waiting. If no handover had been recorded on Friday, Saturday's Today screen would open with L-404 under **Overdue · 13 hr late** instead of hiding it in a list.
- **Lookup bar (press `/`).** When Leela walks up, the clerk types "leela" or reads the tag "AD-12" and lands on L-404. Results cover slips (including who holds them now), units on the shelf and requests. This mirrors the "scan the tag" step real checkout counters use.
- **Tag confirmation on return.** AD-12 and AD-15 share Bin C4, so the return form names the unit by tag and model and points out its look-alike.
- **Slip history.** Each slip records issue, handover and return with the desk time, so the next shift can see what happened without asking.
- **Desk assistant (press `A`).** A chat panel that answers "Who has AD-12?", "What's overdue?", "Leela is here" or "Issue kit for RQ-214" from the live records, and hands the clerk the right button (for example *Return L-404*). It never changes records on its own; the clerk still confirms the tag. It is **rule-based and runs offline**, not a hosted language model. Connecting a real LLM would need a small backend to keep the API key secret, which a public static repo can't do safely.
- **Keyboard shortcuts (press `?`).** `Ctrl`/`⌘` + `K` command menu, `/` lookup, `A` assistant, `T` theme, `1`–`5` switch screens, `N` advance the shift clock, `Z` undo, `Esc` close whatever is open. Counter staff often work with one hand on a scanner, so every common action has a key.
- **Command menu (`Ctrl`/`⌘` + `K`).** One box that lists what needs you now first (for example *Return L-404 · AD-12*), then every screen and action, and also searches records. Keyboard-first, like the command menus in modern product tools.
- **Theme switcher (`T`).** System, light or dark, remembered per browser.
- **Built for phones too.** Below tablet width the side navigation becomes an app-style bottom tab bar with count badges, the assistant opens as a bottom sheet, and buttons grow to full-width touch targets. On desktop the header stays pinned while you scroll.
- **Undo for the last change.** Counter work is rushed. A misclick on *Record return* shouldn't need a manager to fix.
- **Inventory as a shelf map.** Units are grouped by bin, because bins are how the desk finds and reshelves things.

Batch kit issue and the Today screen are secondary to the handover fix. They remove the triple data entry at pickup and the tab-hunting at the start of a shift, but the brief's main problem is the handover, so that is where I focused.

## 4. What I kept

- **One slip per physical unit.** Kit issue and kit check-in are shortcuts that still create and close individual slips, so partial returns keep working exactly as before.
- **The issue guardrails.** Approved types only, one unit per approved type, no units in repair and no double-issuing. Kit issue runs every unit through the same check as the single form.
- **The four original screens and the desk book.** Requests, Inventory, Loan register and Desk book are all still there, and club messages and free-text shift notes still live in the desk book. Today sits in front of them; it doesn't replace them.
- **The zero-dependency setup.** Plain HTML, CSS and JS, offline, with `light-dark()` colours, container queries and system fonts. Saved progress moved to a new storage key (`campus-stores.shift.v3`), and shifts saved by the original app (`v1`) or my earlier draft (`v2`) are migrated on load.

## 5. One observation at a real counter

**When a student returns an item that someone else collected, does the clerk close the right slip from the Today screen or Loan register alone, without opening the Desk book or asking the student which club it was for?**

I would watch for tab switches and questions to the student during those returns. I would also check at the end of each week for slips closed on the wrong unit (for example, AD-15 closed when AD-12 came back). If the change works, the clerk stays on one screen, the question "who did you get this from?" disappears, and wrong-unit closures fall to zero.

## Design notes

- **One loud element.** Asset IDs are drawn as small printed tags with a barcode mark, because the tag on the unit is what the clerk is physically matching. Everything else stays quiet so status colours carry meaning: red for overdue, amber for a handover, blue for out, green for returned.
- **Accessible by default.** Keyboard focus is visible everywhere, the lookup works with arrow keys and Enter, the toast is a polite live region, touch targets grow to 44 px on touch screens, motion respects reduced-motion settings, and the layout works down to phone width in light and dark mode.

## Files

- `index.html`: desk layout, candidate brief, a "What changed" summary, lookup bar, shift stepper and bundled icons
- `app.js`: sample records, Today queue, lookup, desk assistant, command menu, theme switcher, keyboard shortcuts, kit issue, kit check-in, slip handover, history, undo, overdue state and storage migration
- `styles.css`: the design system (tokens, components, responsive and dark-mode rules)
- `original/`: the starting files, unchanged, for side-by-side comparison
- `docs/`: screenshots of the walkthrough

Icon license notices are in [third-party/lucide-LICENSE.txt](third-party/lucide-LICENSE.txt). The extra icons I added (search, check, undo, alert and others) come from the same Lucide set.
