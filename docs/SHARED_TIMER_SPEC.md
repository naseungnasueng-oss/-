# Shared Timer Spec

## Scope and authority

This Spec freezes the adopted shared room timer contract for this integration phase. The observable baseline is the timer snapshot at `06bfbc188c225a4990465856ebe553afdbf130fc`:

- `Assets/Scripts/RoomCountdownTimer.cs` blob `dc2af3c7dadb8dd7c6207598814a692e38d6b55c`
- `Assets/Scripts/RoomTimerSettings.cs` blob `5a6ace32c69193f67cbef7d35159a30e2cf33fa1`
- `Assets/Resources/RoomTimerSettings.asset` blob `ece1de34b0ba20432990a555e357f2938f85a483`

ROOM1/5 consumers and smoke tests from the same snapshot define current use. The baseline is per-room, not cross-room persistent.

## Snapshot facts: baseline observable behavior

- Each participating room owns its timer instance by adding/initializing `RoomCountdownTimer` in that room's controller.
- The timer is initialized with an explicit duration in seconds. ROOM1 and ROOM5 currently pass `12f * 60f` (12 minutes).
- Initialization clamps duration to at least 1 second, sets `TotalSeconds`, sets `RemainingSeconds` equal to `TotalSeconds`, stores optional expiry/retry callbacks, loads `Resources/RoomTimerSettings`, creates runtime UI, refreshes visuals, and starts running.
- Countdown uses unscaled time (`Time.unscaledDeltaTime` / `Time.unscaledTime`), so gameplay countdown and animation do not depend on scaled game time.
- Public observable state includes `RemainingSeconds`, `TotalSeconds`, `RemainingRatio`, `IsRunning`, and `IsExpired`.
- `RemainingRatio` is clamped to 0..1 and reports 0 when total duration is non-positive.
- Visual behavior includes a screen-space timer canvas, text formatted as `남은 시간  mm:ss`, red remaining-time bar, gauge artwork, animated walking character, and a temporary red text flash after a time penalty.
- `DeductSeconds(seconds)` works only while running and not expired. It ignores negative deductions by clamping them to zero, clamps remaining time at zero, flashes penalty feedback, refreshes visuals, and expires if time reaches zero.
- `StopTimer()` stops further countdown without marking the timer expired.
- Expiration happens exactly once. It sets remaining time to zero, stops the timer, marks expired, refreshes visuals, closes the inventory if present, shows the expiry overlay, and invokes the supplied expiry callback if any.
- The expiry overlay is a screen-space overlay with title `시간 종료`, explanatory body text, and a `다시 시작` retry button.
- After expiry, pointer presses on the retry button invoke the supplied retry callback. If no retry callback was supplied, the active scene reloads.
- If `RoomTimerSettings` cannot be loaded or is not ready, initialization logs an error instructing rebuild of settings, creates no timer UI, and does not start running.
- Settings readiness requires gauge artwork and at least one walking frame. The checked settings asset supplies layout, colors, gauge sprite, six walking frames, and animation/layout values.

## ROOM1/5 consumer requirements

- ROOM1 and ROOM5 each start a 12-minute room-owned timer.
- ROOM1/5 stop their timer on room completion/ending so the timer does not expire after success.
- ROOM5 may deduct exactly 60 seconds for the current wrong-desk penalty; deduction must be visible through `RemainingSeconds` and clamped at zero.
- Expiry closes inventory and lets ROOM1/5 run their own expiry callbacks/messages before retry.
- Retry behavior remains room-owned through the supplied callback; current ROOM1/5 retry callbacks remove their own room items and reload the active scene.
- There must not be multiple active timer instances for the same room flow.

## Integration requirements for ROOM2/3 compatibility

- ROOM2/3 may consume the same per-room timer contract without changing their puzzle rules.
- No confirmed ROOM2/3 timer behavior is bound by this Spec yet. This phase therefore does not authorize automatically starting, stopping, deducting, or expiring a timer in ROOM2/3.
- Timer integration must not introduce new penalties, deadline changes, success conditions, navigation behavior, or item consumption into ROOM2/3.
- Any ROOM2/3 timer start/stop/deduct/expiry use must be bound by that room's own confirmed rules/controllers; the timer only provides countdown, visual state, explicit deduction, stop, expiry, and retry surfaces.
- Do not claim or implement cross-room time carryover under this Spec.

## Failure/refusal behavior

- Missing or unready `RoomTimerSettings` refuses to start the timer and reports an error; rooms must not treat that as successful timer readiness.
- Deductions before start, after stop, or after expiry do not mutate time.
- Negative deduction requests do not add time.
- Expiry callbacks/retry callbacks are optional; absent retry callback falls back to active-scene reload.
- Repeated expiry paths must not invoke expiry effects more than once.
- Completion paths must stop the timer instead of relying on hidden UI or scene transition to mask it.

## Acceptance scenarios

- ROOM1 startup creates one `RoomTimerCanvas` and begins a 12-minute unscaled countdown.
- ROOM5 startup creates one shared room timer; a wrong non-Minjun desk move reduces `RemainingSeconds` by at least 59.9 seconds in focused tests.
- When remaining time reaches zero by countdown or deduction, `IsExpired` becomes true, `IsRunning` becomes false, remaining time is zero, inventory is closed, expiry overlay is visible, and the room expiry callback is invoked once.
- Clicking retry after expiry invokes the room retry callback when supplied; without a callback, the active scene reloads.
- Completing ROOM1 or starting ROOM5 ending stops the timer and prevents later expiry for that completed room flow.
- With missing/unready settings, no room claims timer readiness and the error path is observable.

## Evidence / oracles

- Primary baseline: `git show 06bfbc1:Assets/Scripts/RoomCountdownTimer.cs`, `RoomTimerSettings.cs`, and `Assets/Resources/RoomTimerSettings.asset` at the blob IDs above.
- ROOM1/5 consumers: `git show 06bfbc1:Assets/Scripts/Room1GameController.cs`, current ROOM5 controller behavior, and the snapshot ROOM1/ROOM5 smoke tests.
- Focused tests or runtime observations are acceptable independent oracles. Implementation logs alone are not acceptance evidence.

## Deferred choices

Cross-room timer persistence, global time budgets, scene progression timing, save/load, pause menus, performance optimization, and timer art/wording polish are future decisions and are not specified here.
