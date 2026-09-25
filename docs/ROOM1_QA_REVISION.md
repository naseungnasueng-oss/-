# ROOM1 direct-interaction QA revision

Understood as: change ROOM1 presentation and acquisition only, plus the shared inventory layout/initial placement; preserve puzzles and carried-item prerequisites. Do not apply ROOM1's hidden hints/highlights or direct pickup policy to other rooms.

## Confirmed scope

- Hide separate ROOM1 result-message and hover-hint bars, keep the phone's message/gallery content.
- Make ROOM1 hotspot emphasis fully transparent without disabling input targets.
- Let empty drawers close/reopen after collection; do not respawn or reacquire their contents.
- Newspaper inspection has no Back control: clicking outside the paper returns to Desk and consumes that click without activating a desk object.
- USB and photo fragment are direct, one-time pickups with no choice modal. Phone remains a usable field object and cannot be acquired.
- Hide the door's locked/unlocked status label. Successful keypad unlock waits for shared feedback duration, then attempts the completion screen, not direct ROOM2 loading. Existing USB/photo prerequisites and expiration checks remain. The completion screen retains its explicit ROOM2 button.
- Shared inventory becomes 2 columns × 6 rows (12 slots). First opening is lower-right with a margin; subsequent openings retain the dragged position, including room transitions within the persistent session. Keep draggable behavior and existing input ownership. Do not silently discard existing items.

## Verification to perform

Direct pickup and duplicate prevention; phone use without ownership; empty drawer close/reopen; newspaper inside/outside and inventory-covered clicks; hidden bars/highlights/status; delayed success with/without prerequisites and on expiration; 12-slot refusal; initial inventory position and reopen-position retention. Earlier smoke tests and the uploaded ZIP describe the previous behavior, not this revision.
