# Shift Tracker (prototype)

Offline, single-game tracker: which block was on the floor when a goal was scored.
Plain HTML/JS, no build step, no network calls. Data lives in `localStorage` on the device.

## Run locally
Open `index.html`, or serve the folder (`npx serve .`) to also test the service worker (offline mode).

## Deploy on GitHub Pages
1. Create a repo and push these files to the root of `main`.
2. Settings → Pages → Source: *Deploy from a branch* → `main` / `(root)`.
3. Open `https://<user>.github.io/<repo>/` on the phone once while online, then "Add to Home Screen".
   After that it works without signal.

All paths are relative, so it works under the `/<repo>/` sub-path.
Data is per browser and per device. Use Setup → Backup (JSON) before clearing browser data.

## How it works
- **Phases:** start game, end 1st half, start 2nd half, end game.
- **Game clock:** counts up to the half length (default 20:00) and resets at half-time. Pause and resume at any time;
  while paused you can tag the reason (Timeout us / Timeout them / Referee / Injury / Other). Tap the clock to correct it
  to the hall clock. At 20:00 it stops and waits for "End half". Time on ice only runs while the clock runs.
- **Situation:** Power play or Box play opens a sheet: 2 or 5 min, optional player (PP: who drew it, BP: who took it).
  The penalty counts down with the game clock and ends the situation automatically. A 2 min penalty also ends when the
  team without the penalty scores (undoing that goal restores it). One penalty at a time.
- **Goal prompt:** tapping GOAL FOR / GOAL AGAINST records the goal at once and opens a sheet: scorer and assist
  (players on the floor, or "No assist"), and how it started (PP, BP, Faceoff, Turnover, Free hit, Other).
  Close it any time; the goal is already saved and can be completed later from the log.
- **Blocks with more players than on the floor** (4 players, 3 on floor): tap a player to send him off,
  the resting one comes back. The on-floor players are stored with every goal.
- **Stats:** +/- , GF/GA, TOI per block and player, goalie GA, goals by sequence, penalties by player,
  filter by Even / Power play / Box play.

## Porting to React / Angular
The data model is documented at the top of the `<script>`. Suggested split:
- `store` (state + localStorage persistence), `stats` (pure `compute()` function, easy to unit test)
- components: `PhaseCard`, `BlockPicker` (+ `FloorChips`), `SituationCard`, `GoalLog`, `GoalSheet`,
  `StatsTables`, `RosterEditor`, `DataTools`
- time accounting: `settle()` must run before every lineup, goalie, penalty, pause and phase change.
- saved data uses key `shifttracker.v3`; older test data from earlier prototype versions is ignored.
