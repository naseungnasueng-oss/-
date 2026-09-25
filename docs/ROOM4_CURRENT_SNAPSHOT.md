# ROOM4 Current Behavior and Structure Snapshot

**Branch:** `integration/room1-5-shared`

**Snapshot point:** after merging `origin/inventory-implementation` commit `8c3ce07` (`4번방 완료`) into the integration branch.

**Purpose:** preserve current behavior before playtest-driven fixes and later refactoring. This is a snapshot of the implementation as-is, not a target design.

**Verification:** Updated runtime/editor C# direct compilation passes. Actual play, keypad alignment and transition/inventory regression checks remain pending.

Shared behavior and refactor boundary: [Shared snapshot](SHARED_CURRENT_SNAPSHOT.md).

Primary files:

- `Assets/Scripts/Room4GameController.cs`
- `Assets/Scripts/Room4ImageNavigationUI.cs`
- `Assets/Scripts/Room4ImageLibrary.cs`
- `Assets/Scripts/Room4PuzzleSettings.cs`
- `Assets/Resources/Room4ImageLibrary.asset`
- `Assets/Resources/Room4PuzzleSettings.asset`
- Scene: `Assets/Scenes/Room4.unity`
- Art: `Assets/4번방/`

### Runtime structure

- `Room4GameController` owns state and rules:
  - `DrawerOpened`
  - `FilmCollected`
  - `FilmPlacedOnMap`
  - `DoorUnlocked`
  - `RoomCleared`
  - `TimeExpired`
- `Room4ImageNavigationUI` owns the image views, hotspots, drawer keypad UI, door keypad UI, map clue overlay, clear overlay, and next-room button.
- `Room4PuzzleSettings` owns configurable passwords and map clue display settings.
- `DrawerUnlocked` is independent of `DrawerOpened`. The unlocked drawer can close/reopen before and after film acquisition. Back navigation does not close it. Acquisition retires only the film target; reopening the empty drawer never respawns film.
- Right-wall object hotspots survive background changes. While the drawer is open and film is uncollected, use the new `서랍안에 필름있음_QA.png` full scene, copied unchanged from ArtSource; do not draw a second film icon. Film click acquires immediately with existing capacity/duplicate guards. Its target is `(1065,825,360,80)` in reference coordinates.
- The drawer keypad is an object overlay over the wall, with its 3×3 digits 1–9 artwork and separate delete/confirm controls; the zero button is removed. Success shows unlock feedback for one realtime second before returning.
- The top screen-title panel is hidden.

### Current player flow

1. ROOM4 auto-creates `Room4GameController` when active scene name is `Room4`.
2. Controller ensures a camera exists, gets/creates shared inventory, loads `Room4ImageLibrary` and `Room4PuzzleSettings`, creates UI, subscribes to `ItemActivated`, and starts/continues the one-hour shared timer.
3. Player can inspect:
   - overview
   - left wall
   - map
   - right wall
   - first newspaper
   - crossword newspaper
   - W memo
   - X memo
   - memo pad: text-only click message `세상에 공짜는 없다. 시작의 숫자를 순서대로`; no closeup. W/X memo closeups are unchanged.
   - drawer keypad
   - drawer
   - door
   - door keypad
4. Drawer password is read from `Room4PuzzleSettings.drawerPassword` and must be four digits.
5. Door password is read from `Room4PuzzleSettings.doorPassword` and must be four digits.
6. User-confirmed passwords are now configured in the asset: drawer `4657` (the former leading zero is removed), door `8276`. This setting update has not yet been verified in play.
7. Drawer and door keypads submit automatically after four digits. Incorrect input resets; correct input retains the shared unlock feedback. Added drawer confirm/delete buttons and door */# click actions are removed. After drawer unlock feedback, return to the right wall with the open drawer; film click directly acquires.
8. Film collection adds `InventoryItemType.Film` to inventory.
9. Drag film from inventory and release over the enlarged map target. Clicking the map or activating the inventory card no longer applies film. Other items, outside-map releases, inventory-covered positions, completion screens and expired sessions do not consume it.
10. Correct film placement:
    - sets `FilmPlacedOnMap = true`
    - removes `Film` from inventory
    - refreshes the map view
    - shows only the mapped film artwork, without added `Z8`/`Y6` text labels; route controls and film ON/OFF remain
11. Door password success sets `DoorUnlocked = true` only. One-second feedback returns to the door. Using the exit then sets `RoomCleared`, closes inventory and shows the completion screen; the timer keeps running.
12. `RoomExitOverlay` follows ROOM1's completion-screen layout. Only its next-room button calls `MoveToNextRoom()` and loads `Room5` once if available and time remains. No inventory reset or blanket deletion occurs. ROOM3 uses the same exit-confirmation flow before ROOM4.

### Current image/state behavior

- Right wall uses `rightWallDrawerOpened` after drawer opens. User now chose to keep the original wall/memo artwork and existing text-only message for QA; replacement is not a prerequisite for the next demonstration. The rejected interim wall patches remain removed.
- The second newspaper's upper crossword panel opens `CrosswordCloseup`, mapped to `낱말퀴즈_확대.png`, an unchanged runtime copy of `ArtSource/낱말퀴즈.png`. Its region is `(45,72,1450,380)` in the existing 1536×1024 normalized coordinate convention, aligned to the newspaper sprite crop. Back returns to NewspaperTwo, then RightWall. No answer entry or acquisition was added; artwork clues remain unchanged; separate hover/result guidance is now hidden.
- Unity Play Mode smoke reached and passed the new crossword artwork/round-trip assertions, but the full ROOM4 smoke failed later at `Film-on-map state was not saved`. That failure needs separate diagnosis; do not count the full suite as passed. Physical pointer alignment and closeup legibility remain visual QA items.
- Map investigation contract: first valid film drop consumes the inventory film and starts with film ON. Thereafter the map exposes an ON/OFF toggle. `FilmPlacedOnMap` records permanent use; `FilmOverlayVisible` controls only presentation. ON shows `mapWithFilm` without added answer labels and hides routes; OFF shows the original map and permits clicking its ten named buildings to connect a free-form line. Drawing and clearing are available before film use and while OFF, but not while ON. The first map visit seeds a short three-point example (hospital → police → market), with no answer/scoring effect. Clearing it is permanent within this room session; revisits never reseed it. `경로 지우기` clears the entire route; no undo or automatic answer recognition. Switching film or navigating away/back preserves state and route points. Toggle and clear controls are at the lower left. The map's empty hint-bar background is hidden. The missing route renderer was corrected by requiring `CanvasRenderer`. The user confirmed route rendering works after that correction. This confirms visible lines, not all building hit areas, route shapes or an executed automated test.
- Drawer uses `drawerWithFilm` before film collection and `drawerEmpty` after film collection.
- Door and drawer keypad overlays are programmatically created over art-aligned keypad sprites.
- Film artwork: original `Assets/4번방/오른쪽벽 부근/필름.png` has fully opaque alpha (255 throughout); no separate supplied cutout was found. Derived `필름_아이템.png` preserves the original marks with a translucent sheet, subtle edge and transparent 16px margin. `filmItem` and the project builder now prefer this derivative for the field object, inspection and acquired inventory sprite. The inventory card inspects; applying remains drag-and-drop only. Visual acceptance and the user's reported broken rendering remain to be checked in Unity.

### Regression points to verify before refactor

- Verify drawer `4657` and door `8276` from `Room4PuzzleSettings.asset` in actual play, without smoke-test password overrides.
- Film can be placed by map click and by inventory item activation while on map.
- Film activation outside map shows the “not here” message and does not consume the film.
- Film is not duplicated on repeated drawer clicks.
- Clear overlay loads `Room5`.
- Timer/expiration behavior does not create duplicate timer canvases across room transitions.
