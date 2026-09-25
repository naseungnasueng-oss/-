# ROOM3 Unity Implementation Design

**Status:** Approved for implementation. This Design realizes the asset-aligned `docs/ROOM3_SPEC.md`.

## 1. Input binding and authority

Controlling input was verified before drafting:

- Path: `docs/ROOM3_SPEC.md`
- Commit: `3f61e2f1a4eea587410c0253fa4cb137140d3f9f`
- Working-tree sha256: `fde514fa0a160eb049f5bea74deb0fda2ad39ebf5c0c73c4cb869ca749942829`
- `git show 3f61e2f:docs/ROOM3_SPEC.md | sha256sum`: `fde514fa0a160eb049f5bea74deb0fda2ad39ebf5c0c73c4cb869ca749942829`
- Branch inspected: `room2-minju`

`ROOM3_SPEC.md` is the only product authority for this Design. Existing ROOM1/ROOM2 code and tests are implementation evidence only. `docs/ROOM2_ROOM3_IMPLEMENTATION_PLAN.md` is known stale for ROOM3 and must not override the Spec.

## 2. Goals and non-goals

### Goals

- Implement a standalone playable ROOM3 placeholder that preserves every observable contract in Spec §§3-9.
- Keep puzzle meaning in room-specific Rules/Controller code while extracting only earned shared seams.
- Preserve ROOM2 conventions: progress facts separate from sprites, stateful source objects separate from revealed content layers, fixed inventory drag/drop for item-to-target use, one-time acquisition, observable refusal, reinspectable clues, and item-family separation.
- Provide clear module seams for tests at Rules, Controller, View adapter, inventory transaction, and smoke/player-path layers.

### Non-goals

- No ROOM4 gameplay design for the notice.
- No timer/save/load/persistence redesign; only integration seams are named.
- No blocking final-art pass, ads, real scene transition implementation, or universal room-object base class. Supplied art is used where compatible; missing or unsuitable art receives a localized rough render and enters the later asset-request list.
- No changes to ROOM1/ROOM2 observable outcomes or puzzle meaning. Shared-mechanism extraction may refactor their code and tests while preserving behavior; ROOM1/ROOM2 assets, settings, `.vscode/`, `Ghost_Game.slnx`, and the Spec remain unchanged.

## 3. Architecture and ownership

ROOM3 should follow the proven ROOM2 split but avoid copying incidental code wholesale.

| Layer | Owns | Must not own |
|---|---|---|
| `Room3Rules` | Domain facts, puzzle transitions, prerequisite checks, refusal/result values, solution constants | Unity UI, sprites, pointer coordinates, inventory storage implementation |
| `Room3GameController` | Inventory transactions, orchestration of Rules transitions, item-to-target adapters, cross-room inventory precondition checks, view-facing typed queries | Sprite decisions, hit testing, hidden answer leakage in UI text |
| `Room3View` | Runtime UI, contextual surfaces/history/Back, hotspots, field choice modal, drag release target resolution, lock/calendar controls, placeholder rendering | Puzzle truth, item consumption decisions, combination rules |
| `InventoryManager` | Generic inventory add/remove/select/drag/card presentation primitives | ROOM3 old-photo puzzle rules or album-discovery gating |
| Shared small modules | Mechanically repeated primitives proven by ROOM2/ROOM3 | Room-specific meanings or story facts |

### Planned file/module shape

Implementation should add these files in phases:

- `Assets/Scripts/Room3/Room3Rules.cs`
- `Assets/Scripts/Room3/Room3GameController.cs`
- `Assets/Scripts/Room3/Room3ImageLibrary.cs`
- `Assets/Scripts/Room3/Room3View.cs`
- `Assets/Scripts/Room3/Room3Bootstrap.cs`
- `Assets/Scripts/Shared/CyclingLock.cs`
- `Assets/Scripts/Shared/ContextualImageSurface.cs`
- `Assets/Scripts/Shared/FieldChoiceModal.cs`
- `Assets/Scripts/Shared/InventoryDragTargetResolver.cs`
- Editor tests under `Assets/Editor/Room3*.cs` plus shared-module interface tests.

Do not introduce a universal room-object base class. The ROOM3 object catalog is too puzzle-specific; shared code should be small mechanical seams, not inheritance over room semantics.

## 4. Domain model

### Core enums and value types

Use explicit typed state instead of stringly progress facts.

- `Room3ActionResult`: `Success`, `AlreadyComplete`, `MissingPrerequisite`, `IncorrectInput`.
- `Room3ItemUseTarget`: `LetterBoard`, `AlbumCutout`, `Mirror`, plus target values only if direct item drop is needed.
- `Room3BoardLetter`: `A`, `L`, `B`, `U`, `M`.
- `Room3LetterSource`: `SofaCushion`, `CoffeeTableMagazine`, `PictureFrame`, `TvCabinetCompartment`.
- `Room3Direction`: `Up`, `Right`, `Down`, `Left`. The cabinet lock uses `char`, allowing the five solution letters and four decoys without polluting the board-letter type.
- `Room3CalendarValue`: immutable `Year`, `Month`, `Day`.
- `Room3Surface`: contextual surfaces, e.g. `Overview`, `BoardCloseup`, `ChinaCabinetLock`, `ChinaCabinetOpen`, `ActivityAlbum`, `CompletedPhotoCloseup`, `DrawerLock`, `DrawerOpen`, `MirrorCloseup`, `CalendarCloseup`, `ExitDoor`.
- `Room3FieldChoice`: collectible/inspectable choices such as individual letters, tissue, notice, album inspection.

### Required `Room3Rules` facts

`Room3Rules` should store the Spec §7 facts directly:

- Four source moved/open facts and four letter revealed facts.
- `WasLetterAcquired` and `IsLetterFixed` progress for `L/B/U/M`; `A` is fixed from initialization. `WasLetterAcquired` means the one-time source pickup completed, never current possession. Actual current ownership remains in inventory and is queried by the Controller.
- `BoardNextLetterIndex` over the sequence `L,B,U,M`; `IsBoardComplete` derived from all fixed movable letters.
- A composed `CyclingLock<char>` holding the cabinet-lock value/solution/retirement state; cabinet unlocked.
- Album available, cut-out discovered, old-photo combination enabled.
- Combined photo created; combined photo placed; completed-photo inspected.
- Completed-photo gaze arrangement inspected without a rendered text answer.
- A composed `CyclingLock<Room3Direction>` holding the direction-lock value/solution/retirement state; drawer unlocked; drawer open/closed.
- Notice acquired and reinspectable from inventory in ROOM3.
- Tissue acquired, tissue consumed, mirror cleaned into a non-interactive result.
- Calendar value, exit open, completion/transition requested.
- Cross-room left/right old-photo availability is queried through controller inventory, not faked inside Rules.

### Transition table

| Request | Preconditions | State changes | Refusal |
|---|---|---|---|
| Move/open letter source | Source not already moved/open | Source moved/open = true; matching letter revealed = true | Already moved/open returns `AlreadyComplete` and preserves reveal |
| Collect revealed letter | Source revealed, not already owned/fixed | Add matching inventory item through controller, then set owned fact | Hidden source -> `MissingPrerequisite`; duplicate -> `AlreadyComplete` |
| Place board letter | Owns current expected letter | Remove carried letter, fix it, advance expected index; if all fixed, board completes and its hotspot retires | Wrong, out-of-order, unowned, already fixed, unrelated -> no mutation/loss |
| Change cabinet lock position | Cabinet not unlocked and target not retired | Shared `CyclingLock` advances/reverses one position; auto-evaluates | Retired/unlocked -> ignored/refused without relock |
| Cabinet lock reaches `ALBUM` | Exact value `A/L/B/U/M` | Cabinet unlocked = true; lock retired = true; unlock feedback event | Any other value remains adjustable |
| Open/inspect album | Cabinet unlocked | Album available/reinspectable = true; cut-out discovered = true; combination enabled = true | Locked cabinet -> `MissingPrerequisite` |
| Combine old photos | Cut-out discovered; inventory has actual left and right pieces | Transaction removes both pieces, adds combined item, and closes the obsolete input card/detail | Before discovery, missing piece, sink photo, or unrelated item -> no mutation |
| Place combined photo | Cut-out discovered; combined carried; pointer released on the shared album photo-frame region | Remove combined item; placed = true; the same region becomes completed-photo inspection | Separate piece/wrong item/wrong target -> no mutation |
| Inspect completed photo | Combined photo placed | Completed photo inspected = true; image-based gaze clue remains available without a text answer overlay | Before placed -> no gaze solution |
| Change direction lock | Drawer not unlocked and target not retired | Shared `CyclingLock` advances/reverses one position; auto-evaluates | Retired/unlocked -> ignored/refused without relock |
| Direction lock reaches `↑↓←↓→` | Exact five-position value | Drawer unlocked = true; direction target retired = true; View holds `풀렸다` for one second, clears it, then returns to CenterWall | Any other value remains adjustable |
| Open drawer | Drawer unlocked; currently closed | Drawer open = true; navigate from CenterWall drawer region to open-drawer closeup | Locked -> `MissingPrerequisite`; already open -> stable |
| Collect notice | Drawer unlocked and open; not already acquired | Add notice once; notice acquired = true; open drawer switches to empty art without auto-reading | Locked/closed -> `MissingPrerequisite`; duplicate -> `AlreadyComplete` |
| Collect tissue | Tissue not already acquired/consumed | Add tissue once; tissue acquired = true | Duplicate -> `AlreadyComplete` |
| Use tissue on mirror | Owns tissue; mirror not cleaned | Remove tissue; tissue consumed = true; mirror cleaned = true; View switches art and retires mirror hotspots | Wrong target or missing tissue -> no mutation |
| Set calendar | Real date in 2014-2016 | Set value; if `2015/05/20`, exit open = true and View returns to RightWall after one-second feedback | Wrong valid date leaves exit closed and adjustable; impossible date is not committed |
| Use exit | Exit open | Mark ROOM3 completion/transition requested; placeholder reports ROOM4 seam | Closed -> refused |

## 5. Sequential letter-board drop resolution

The board accepts only inventory drag/drop; no alternate selected-item path should become normal ROOM3 play. Use `Room3GameController.UseItemOnTarget(item, Room3ItemUseTarget.LetterBoard)` from the view's trusted release adapter.

Inventory item types should be added for `Room3LetterL`, `Room3LetterB`, `Room3LetterU`, `Room3LetterM`. `A` is never inventory. `Room3Rules.ExpectedBoardLetter` returns the next movable letter. Controller mapping is exact:

- `Room3LetterL` -> `L`
- `Room3LetterB` -> `B`
- `Room3LetterU` -> `U`
- `Room3LetterM` -> `M`

Transaction order for a valid board placement:

1. Validate item identity and current expected letter against `Room3Rules`.
2. Validate inventory has exactly the carried item.
3. Remove the item from inventory.
4. Apply the Rules placement transition.
5. Refresh view and show placement feedback.

If step 3 fails, do not mutate Rules. If Rules unexpectedly refuses after removal, re-add the same item immediately and report a controller invariant failure in tests; production should avoid this by prechecking.

## 6. Old-photo combination and inventory transformations

Do not put ROOM3 album-discovery rules into `InventoryManager`. Preserve the existing inventory gesture—select one slot, then select the other—and replace the hardcoded newspaper-only branch with an owner-scoped `IInventoryCombinationPolicy` seam proven by two adapters: the existing ROOM1 newspaper combination and the ROOM3 old-photo combination.

The policy interface receives the two selected item types and returns a typed handled/success/refusal result containing the committed replacement type on success. `InventoryManager` exposes one generic atomic `TryReplacePair(first, second, replacement, replacementSprite)` primitive that mutates inventory but does not emit completion itself. The ROOM1 newspaper adapter and `Room3GameController` call that primitive; InventoryManager never learns newspaper, album, cut-out, or old-photo meaning. ROOM3 registers/unregisters its Controller-backed policy for the room lifetime. After the policy returns success, the outer slot-selection flow emits `ItemCombined(replacementType)` for presentation observers.

Because current `TryRemoveItem` refuses duplicate item types, ROOM3 requires distinct item types for left/right pieces: existing `PhotoFragment` is left, existing `RightOldPhotoFragment` is right. Add `CombinedOldPhoto` for the replacement. Selecting the pair before cut-out discovery or selecting any other pair does not combine them and falls back to ordinary slot selection/card behavior.

Transaction order for valid combination:

1. On second-slot selection, InventoryManager asks the active policy to handle the unordered pair.
2. `Room3GameController` checks cut-out discovery and confirms the exact `PhotoFragment` + `RightOldPhotoFragment` pair.
3. The Controller calls `TryReplacePair`; InventoryManager preflights both distinct entries and replacement capacity, then atomically replaces them with `CombinedOldPhoto`. A failed preflight mutates nothing.
4. On success, control returns directly to the same Controller call, which records `Rules.CombinedOldPhotoExists`; no event subscription is required for domain synchronization.
5. The Controller returns a successful policy result to the outer InventoryManager slot-selection flow, which then emits `ItemCombined(CombinedOldPhoto)` for presentation observers.

Sink photos `SinkPhoto1-4` and unrelated items are never accepted as substitutes. The existing newspaper adapter uses the same transaction module, preserving ROOM1 combination behavior without exposing ROOM3 meaning.

Album placement transaction:

1. Validate target is discovered cut-out and item is `CombinedOldPhoto`.
2. Remove `CombinedOldPhoto`.
3. Set `CombinedOldPhotoPlaced` and completed-photo availability.
4. Refresh album surface. Wrong target preserves ownership.

## 7. Contextual view, surfaces, history, and field choice modal

### Contextual image surfaces

Before creating `Room3View`, extract the proven ROOM1/ROOM2 navigation mechanism into `ContextualImageSurface`: typed surface key, history stack, Back, hotspot registration, enabled predicates, highlight clearing, and refresh after pointer-gesture release. ROOM1 and ROOM2 become its first two adapters and must retain their existing player behavior before ROOM3 is added.

`Room3View` then supplies ROOM3 surfaces, background selection, predicates, and actions through that small interface. The shared module must not know puzzle facts, room enum values, Sprites chosen by state, or messages. Each room still binds a stable image frame, state/content layers, and Controller-owned predicates.

### Independent source/content layers

For letter hiding objects, background art must not own truth. Each source object has two independent view elements:

- source presentation: unmoved/moved or closed/open Sprite variant or localized patch chosen from Controller state while keeping the same hotspot coordinates;
- content item layer: letter Sprite/hotspot visible only when revealed and not acquired/fixed.

Clicking a source opens the shared field modal. Cushion, magazine, and frame use `확인한다 / 움직인다 / 취소`; the TV compartment uses `확인한다 / 연다 / 취소`. The Controller mapping follows the supplied art exactly: frame reveals `L`, TV compartment reveals `B`, cushion reveals `U`, and magazine reveals `M`. The successful action changes the persistent source state and reveals the letter. Acquiring a letter hides only the content item layer; the source presentation remains moved/open.

### Field choice modal

Before `Room3View`, extract the ROOM1/ROOM2 modal shell as `FieldChoiceModal`. Its interface binds title, description, three action labels/callbacks, and open/close state; its implementation owns blocker/raycast behavior and button layout. ROOM3 uses it for source movement/opening, revealed-letter/tissue/notice acquisition, and clue inspection. Puzzle-specific labels, eligibility, and callbacks remain in `Room3View`/Controller.

## 8. Trusted inventory drag-release target resolution

Keep the existing trusted route: `InventoryManager.ItemDragReleased(type, screenPosition)` -> current room view resolves contextual target -> controller handles meaning.

ROOM3 view should:

- Subscribe/unsubscribe on initialize/destroy.
- During inventory drag, block room click gestures and show only compatible active target highlights.
- On release, ignore ordinary hotspot button processing and resolve target by active surface + active target rect + screen position.
- Return `WrongItem` with observable refusal when no target is found.
- Never infer future clues or hidden answers from the dragged item.

Extract `InventoryDragTargetResolver` from ROOM2 before the first ROOM3 View drop path. Its small interface accepts active target rectangles plus a compatibility predicate and returns a typed target for the trusted release position; it also drives compatible-target highlighting. ROOM2 is the preserving adapter, while ROOM3 supplies board, album-cutout, and mirror targets. Puzzle meaning remains in each room Controller.

## 9. Shared deep cycling-lock module

ROOM3 contains two cycling locks, so a reusable mechanical module is earned. Use one pure C# generic `CyclingLock<TCandidate>` in `Assets/Scripts/Shared/CyclingLock.cs`. `Room3Rules` composes `CyclingLock<char>` for the cabinet and `CyclingLock<Room3Direction>` for the drawer, keeping their current values and automatic completion in the domain layer.

### Shared responsibilities

- Candidate order per lock.
- Current value array.
- Per-position next/previous cycling with wrap.
- Auto-evaluation after each position change.
- `IsSolved` and `IsRetired` flags.
- Retired-target behavior: position changes return `AlreadyComplete`/`Retired` without mutation.
- Unlock feedback seam: return a transition result carrying `UnlockedNow` so the Room3 controller/view can show cabinet/drawer-specific feedback.

### Shared interface sketch

Keep the interface small and testable while hiding cycling, wrap, solution, and retirement behavior in the module:

- Constructor: candidates, initial values, solution values.
- `IReadOnlyList<T> Values`
- `CyclingLockStepResult Cycle(positionIndex, int delta)`
- `bool IsSolved`
- `bool IsRetired`
- Solving automatically sets `IsRetired`; there is no caller-controlled retirement option.

`CyclingLock` must not know cabinet, drawer, ALBUM, gaze, sprites, or messages.

### Cabinet lock adapter

Design-owned concrete choices:

- Candidate pool: nine candidates, using four decoys.
- Decoys: `C`, `D`, `E`, `R`.
- Shared candidate order: `A, L, B, U, M, C, D, E, R`.
- Solution: `A, L, B, U, M`.
- Initial value: `C, D, E, R, C` (deterministic and not `ALBUM`).
- Controls: primary interaction is a short vertical drag/swipe on one ring, snapping one candidate per threshold step; upper/lower click zones provide previous/next fallback. Keyboard is test-only.
- Evaluation: after each accepted drag/click step; no submit.
- Unlock feedback: control rects retire immediately; the View holds `풀렸다` on the solved lock for one second, clears the message, then returns to CenterWall where the opened cabinet and album hotspot are available.
- Optional sound/animation may supplement but not replace that fixed transition.

### Direction lock adapter

Design-owned concrete choices:

- Candidate pool/order: use the same directional order everywhere: `Up, Right, Down, Left`.
- Five-position solution: `Up, Down, Left, Down, Right` (`↑ ↓ ← ↓ →`), matching the supplied five-slot lock and the five-person left-to-right gaze clue.
- Initial value: `Up, Up, Up, Up, Up` (deterministic and not solution).
- Controls: the same vertical drag/swipe plus upper/lower click fallback as the cabinet lock.
- On solution, controls retire and the View holds `풀렸다` for one second before returning to CenterWall with the drawer still closed. Clicking the unlocked drawer region explicitly opens its notice/empty closeup.

The candidate order is shared in the sense that every position within a lock uses one order, and both adapters use the same module semantics. Letter and direction candidate sets remain distinct by type.

## 10. Calendar design

Use an eight-digit seven-segment numeric control.

- Valid range: real dates from `2014-01-01` through `2016-12-31`, including month-length validation.
- Initial value: fixed date `2014 / 08 / 03`, intentionally far from both dates printed on the notice.
- Controls: `▲` increases and `▼` decreases the selected digit; invalid intermediate calendar dates are skipped rather than committed.
- Evaluation: after every accepted digit change; no submit button.
- Exit opens only when value equals `2015 / 05 / 20`. The View then disables controls, holds `풀렸다` for one second, clears the message, and returns to RightWall.

## 11. Mirror and completed-photo presentation

- `IsMirrorCleaned` records successful tissue use. The View immediately switches to the supplied cleaned-mirror image and retires both mirror hotspots; only Back remains.
- `IsCompletedPhotoInspected` records close inspection of the completed group photo. The photo art itself communicates the five gazes; the View must not render the direction answer as a green sentence or glyph sequence.
- Legacy evidence/match facts may remain internal for compatibility, but no post-cleaning mirror inspection path is exposed by the current product contract.

## 12. Notice inventory presentation and ROOM3-only reinspection

Add `StudentInterviewNotice` as an inventory item. Its default catalog entry may be generic enough not to imply ROOM4 use. ROOM3 view/controller should install a ROOM3-scoped presentation override while active:

- Display name: `학생 면담 통지서`.
- Short description: identifies it as a ROOM3 clue document.
- Detail text includes both labels distinctly: creation date `2015-05-17`, actual interview date `2015-05-20`.
- Before acquisition, the drawer modal's `확인하기` opens the notice without changing ownership; `획득하기` adds it without auto-reading.
- After acquisition, Action `Read` opens the ROOM3 notice surface from inventory and remains repeatable while the item is owned. No room reinspection hotspot duplicates this path.

Clear the override on destroy, following ROOM2's phone override pattern. Do not add ROOM4 semantics.

## 13. ImageLibrary, asset slots, fonts, arrows, and placeholders

`Room3ImageLibrary` should be a `ScriptableObject` with replaceable slots only. Null art must not block a playable placeholder.

Bind supplied art from `Assets/Art/Room3/` where available:

- Stable surfaces and state views: `Views/Overview`, `LeftWall`, `CenterWall`, `RightWall`, `CoffeeTable`, `Album`, and `Locks`. Letter placement is performed directly on the stable `CoffeeTable` base; `LetterBoard` is retained only as a compatibility enum/source-art grouping, not a player navigation surface.
- Source state views: frame→`L` and cushion→`U` under `LeftWall`; TV compartment→`B` under `RightWall`; magazine→`M` under `CoffeeTable`.
- Board progression: supplied `A`, `AL`, `ALB`, `ALBU`, and `ALBUM` whole-surface variants.
- Runtime controls use the blank five-position cabinet lock, blank five-position direction lock, and blank calendar backgrounds; glyphs remain Controller/View-owned overlays.
- `Layers/Interactables` and `Layers/Letters` provide independently controllable source/content layers where a whole-state image is unsuitable.
- `Items/room3_combined_old_photo.png` is usable; the tissue source remains unsuitable until its white background is removed.
- `Reference/PrefilledControls` is visual reference only and must not be bound as live lock state.
- Feedback hooks may remain null.

Missing-final-art registry for later polish: open exit, transparent tissue item, and any rough-rendered state discovered during player-path QA. Clean-mirror and student-interview-notice art are already mapped.

Null-art behavior:

- Surface background null -> render a dark panel with the surface title.
- Patch sprite null or known-unsuitable -> render a labeled translucent placeholder localized to the object bounds.
- Inventory sprite null or known-unsuitable -> item still appears by label in inventory with a simple generated icon.
- Font null -> use runtime dynamic Korean-capable font selection already proven in ROOM2.
- Arrow sprite null -> use text arrows.

## 14. Error/refusal handling

Refusals must be observable but not revealing. Use stable message categories:

- Wrong item/target: `그 물건으로는 맞지 않는다.`
- Missing prerequisite: `아직 필요한 조건이 갖춰지지 않았다.`
- Already complete: `이미 처리했다.` or a clue-specific reinspection message.
- Locked object: `잠겨 있다.`
- Calendar wrong date: no success feedback; optionally `아직 문은 열리지 않는다.`

Do not show hidden answer strings before the player has earned them. `ALBUM`, `↑ ↓ ← ↓ →`, and the actual interview date may appear only through their earned surfaces/items or controls set by the player.

## 15. Test strategy

### Module/interface tests

- `CyclingLockBatchTest`: candidate order, deterministic initial values, next/previous wrap, auto-unlock on solution, no mutation after retirement, invalid index refusal.
- `Room3RulesBatchTest`: all transition-table requests, board order refusals, drawer open/closed facts, lock retirement, and real-date calendar boundaries.
- `Room3ControllerBatchTest`: inventory add/remove transactions, old-photo policy gating and atomic replacement/card cleanup, sink-photo refusal, board letter removal ordering, tissue consumption, and notice acquisition only from an open drawer.
- `Room3ViewBootstrapBatchTest`: placeholder UI creates surfaces/hotspots without art; modal blocks raycasts; Back history works.
- `ContextualImageSurfaceBatchTest`, `FieldChoiceModalBatchTest`, and `InventoryDragTargetResolverBatchTest`: exercise each extracted module through its small interface plus ROOM1/ROOM2 preserving adapters.
- `Room3InventoryDragBatchTest`: release adapter resolves board, the unified album photo-frame region, and mirror; invalid releases do not mutate; highlights match compatible targets.

### Batch smoke/player path

Add an editor smoke that starts with both actual old-photo pieces carried and executes the Spec happy path:

1. Reveal/acquire all letters in any source order.
2. Place board letters in strict order.
3. Solve cabinet lock to `ALBUM` through cycling controls.
4. Inspect album/cut-out.
5. Combine old-photo pieces and place combined photo.
6. Inspect the completed photo without rendering the direction answer as text.
7. Solve the five-position direction lock; verify one-second feedback, CenterWall return, and explicit drawer opening.
8. Preview the notice without acquisition, acquire it without auto-reading, then reread it from inventory.
9. Set `2015/05/17` and verify closed; set `2015/05/20` and verify one-second feedback, RightWall return, and open/completion state.

Add a separate mirror smoke for tissue consumption, cleaned-image replacement, and hotspot retirement.

### ROOM1/ROOM2 regressions

Run existing editor batches after ROOM3 integration:

- `Room1BatchSmokeTest`
- `InventoryDragBatchTest`
- `Room2RulesBatchTest`
- `Room2ControllerBatchTest`
- `Room2InventoryPolicyAndAffordanceBatchTest`
- `Room2PlayerNavigationViewTest`
- `Room2ViewBootstrapBatchTest`

Add continuity checks that ROOM2 keypad `3412` remains independent of ROOM2 old-photo branch, USB remains consumed by computer use, left/right old-photo pieces remain distinct at ROOM3 entry, and missing-piece policy blocks or permits recovery without fabrication.

### Manual QA oracle

Manual QA should capture visible evidence, not success logs alone:

- Initial fixed `A`, hidden letter layers, persistent source-object movement, and one-time letter acquisition.
- Board refuses wrong order; completing the board alone leaves the cabinet locked, and only setting the separate cabinet lock to `ALBUM` unlocks it.
- Cabinet lock has exactly nine candidates per position, initial `CDERC`, and no full alphabet.
- Album title/page/cut-out identity, post-discovery photo combination, and sink-photo refusal.
- Direction clue is absent before completed-photo placement and remains image-only after inspection; no green answer sentence appears.
- The five-position direction lock reaches `↑↓←↓→`, holds feedback for one second, returns to CenterWall without opening the drawer, and retires.
- Clicking the unlocked drawer region opens it; notice preview does not acquire, acquisition does not auto-read, and inventory reinspection shows both labeled dates.
- Mirror cleaning consumes tissue, switches art, retires hotspots, and does not gate exit.
- Calendar creation date stays closed; actual date opens exit automatically.

## 16. Phased implementation and commit order

Use small commits in this order; each phase should pass its named tests before the next phase starts.

1. **Common ROOM1/ROOM2 mechanism extraction:** extract contextual surface/history/hotspots, field-choice modal, and trusted inventory drag-target resolver in separate commits, preserving both room smoke paths after each extraction.
2. **Shared lock module:** add `CyclingLock` and tests. No ROOM3 scene dependency.
3. **ROOM3 Rules:** add state model and transition tests for board, album/photo, mirror, notice, calendar, and exit.
4. **Inventory item and combination seam:** add `Room3LetterL/B/U/M`, `CombinedOldPhoto`, `CleanTissue`, `StudentInterviewNotice`; replace newspaper hardcoding with the owner-scoped pair-combination policy and preserve ROOM1 behavior.
5. **ROOM3 Controller:** implement inventory transactions and target adapters; test success/refusal/rollback.
6. **Contextual View shell:** add bootstrap, surfaces, Back, placeholder ImageLibrary, and shared modal adapter; no puzzle completion yet.
7. **Letter board path:** source/content layers, letter acquisition, board drag target, board tests/smoke slice.
8. **Cabinet lock and album discovery:** first `CyclingLock` adapter, cabinet unlock feedback/retirement, album/cut-out reinspections.
9. **Old-photo combination and placement:** ROOM3 combination-policy adapter and album cut-out drag target.
10. **Direction lock and notice:** second `CyclingLock` adapter, notice acquisition/reinspection override.
11. **Mirror branch:** tissue acquisition/drop, cleaned/evidence/match facts for both orders.
12. **Calendar and exit placeholder:** controls, auto-open, completion seam with no real ROOM4 transition.
13. **Regression and manual QA pass:** run ROOM1/ROOM2/Inventory tests and correct the owning shared or ROOM3 module when a regression is found.

The three common interaction modules are prerequisites to `Room3View`, not to pure Rules or `CyclingLock`. `CyclingLock` is a prerequisite to either ROOM3 lock because two adapters are already known in the same room.

## 17. Unresolved integration seams

These remain explicit seams and must not block standalone ROOM3 placeholder play:

- Cross-scene inventory/progress persistence lifetime.
- Owner-approved old-photo recovery policy: ROOM2 transition gate versus revisit/backtracking.
- Shared timer behavior, if any.
- Real ROOM4 transition implementation.
- Later-room use or non-use of the interview notice.
- Final art/audio/animation production and the post-functional missing-asset request.
- Save/load serialization.
- Advertisement/rewarded-ad hint systems.

Standalone placeholder behavior: when exit is open and used, show `ROOM3 완료. ROOM4 전환 지점입니다.` or equivalent placeholder feedback, set a completion fact, and do not attempt real scene navigation until a ROOM4 seam is approved.

## 18. Spec traceability

| Spec area | Design realization |
|---|---|
| §3 ROOM2 conventions | Rules facts separate from sprites; source/content layers; fixed inventory drag; one-time AddItemOnce; refusals; ROOM3 presentation overrides |
| §4 Observable flow | Transition table and phased smoke path preserve exact dependency graph |
| §5 Object catalog | Domain facts, surfaces, source layers, inventory item additions, lock/calendar modules cover all listed objects |
| §6.1 Letter board | Strict expected-letter state, board target adapter, per-letter inventory removal |
| §6.2 Cabinet lock | Shared cycling lock, nine candidates, decoys `C/D/E/R`, initial `CDERC`, auto-unlock/retire |
| §6.3 Album discovery | Cabinet-unlocked album surface sets cut-out discovered and reinspection facts |
| §6.4 Old photos | ROOM3-owned combination policy gated by cut-out discovery; sink photos rejected; placement consumes combined item only |
| §6.5 Gaze clue | Completed-photo inspection exposes the five-person left-to-right sequence `↑ ↓ ← ↓ →` and remains reinspectable |
| §6.6 Direction lock | Second cycling-lock adapter with five positions, initial `↑↑↑↑↑`, auto-unlock/retire |
| §6.7 Notice | `StudentInterviewNotice` one-time inventory item with ROOM3-scoped readable presentation |
| §6.8 Mirror | Separate cleaned/evidence/photo/match facts support either branch order and non-gating exit |
| §6.9 Calendar/exit | Year/month/day controls, `2015/05/17` trap closed, `2015/05/20` auto-opens |
| §7 Progress facts | Stored in `Room3Rules` or queried as actual inventory ownership; no sprite truth |
| §8 Failures/repeats | Result enums, transaction ordering, retired-target behavior, no duplication/rollback |
| §9 Acceptance/oracles | Automated module/smoke/regression tests plus manual QA evidence list |
| §10 Open questions | Preserved as unresolved integration seams, not silently decided |
| §11 Design fence | Design chooses modules, initial values, decoys, controls, placeholders, and tests only |
