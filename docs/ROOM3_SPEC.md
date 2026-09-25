# ROOM3 Gameplay/Product Specification

**Status:** Confirmed implemented ROOM3 gameplay/product baseline. Remaining room-local work is presentation and hotspot optimization; Unity structure remains in `ROOM3_DESIGN.md`.

## Current coffee-table / direct-pickup QA amendment

Understood as: extend direct field acquisition to ROOM3, place the magazine opposite the tissue box at a similar vertical position, move it downward to uncover M, and fit the puzzle-progress cutout to the existing tabletop puzzle. This overrides conflicting older acquisition descriptions below; other investigation/lock/navigation rules are unchanged.

- Exposed L/B/U/M pieces, tissue and the earned interview notice are acquired on click without inspect/acquire/cancel choices. Existing prerequisite, capacity and duplicate checks remain. Owned notice reading remains available through inventory. Source-object movement and non-collectible evidence investigation are not globally converted to pickups.
- Magazine starts at `(1050,285,240,284)` opposite the tissue `(230,285,190,290)`. Its existing move action slides it downward 240 reference pixels to y=525, without rotation. M and its hotspot move with the magazine's initial hiding location, and remain exposed above the moved magazine. Do not acquire M merely by moving the magazine.
- Board progression art contains a larger board than the coffee-table background despite sharing a 1536×1024 canvas. Sample its `(436,256,664,512)` board region and render all four progression states into tabletop bounds `(469,320,517,398)`. Source art is preserved; image-canvas equality is not proof of visual registration.
- Updated letter/notice/tissue/playable-path test expectations cover direct acquisition, downward magazine pose/revisit, and board crop/destination geometry. Actual Play Mode and visual seam/alignment QA remain pending; compilation is not visual acceptance.

### Notice dismissal and commit checkpoint

- After acquisition opens the drawer/notice artwork, clicking outside the paper `(440,30,655,925)` returns to the empty open drawer. Inside-paper clicks keep it open. Preserve notice ownership and consume the dismissal gesture so it cannot also close the drawer. Inventory-covered/consumed input retains priority. Inventory-initiated reading returns to its prior surface rather than fabricating a drawer visit.
- Shared timer styling is separately tuned in `RoomTimerSettings`: red bar `(0.52,0,0.01,1)`, panel alpha 0. Digits, gauge artwork, walker, remaining-time calculation and expiration behavior are unchanged.
- Runtime/editor source compilation is the automated verification available in this session. Revised scene/gesture tests are authored, not executed; older album/mirror modal-visibility tests still require reconciliation. No claim of full regression or hosted-build acceptance.

### Latest QA follow-up: direct movement and drawer reveal

- Picture frame, sofa cushion, TV compartment and magazine source clicks directly perform their existing move/open action with no choice modal; revealed letters still require a separate pickup. Mirror clicks directly inspect only when cleaned, retaining the tissue-cleaning prerequisite and evidence rules.
- Direction-lock success finishes its shared feedback delay on the open drawer/notice view, not by backing out to CenterWall. Expiration still blocks this automatic opening.
- Notice acquisition shows `ArtSource/Room3/Incoming/3번방/3번방_가운데벽_서랍확대_면담통지서.png`, copied unchanged to `Assets/Art/Room3/Views/CenterWall/room3_drawer_notice_closeup.png` and mapped through `studentInterviewNotice` in the library and builder. Original/reference artwork remains untouched.
- These rules supersede older source-modal and manual-after-unlock drawer steps below. Playable-path expectations have been updated; broader legacy modal tests remain to be reconciled/run.

### Follow-up: album targets, silent interactions and date display

- On the unlocked center-wall cabinet, only the visible album/book area `(470,435,72,90)` opens the album, not the whole cabinet.
- Merely opening the album does not earn inspection/cutout discovery. Clicking its investigation area directly calls the existing investigation rule without a choice window. Photo combination remains blocked until this explicit investigation; photo placement remains a separate inventory drop. Completed-photo inspection is likewise direct on click, preserving its evidence gate.
- Hide hover/context/result guidance and added album/cutout/completed-photo/face-comparison explanatory text. Preserve the actual artwork, readable notice and operational controls. Do not replace hidden prose with automatic puzzle completion.
- TV cabinet compartment opens on click without a source-choice modal; its exposed B still requires a separate pickup click.
- Successfully acquiring the interview notice immediately opens its enlarged notice surface, without another choice. Back returns to the empty drawer closeup; failed acquisition does not open the preview.
- The date device, not the shared timer, starts at `0000-00-00` with white seven-segment digits. Individual digits allow partial/invalid intermediate input; only a valid in-range date is submitted to the existing Rules. Exit answer remains `2015-05-20`. The one-hour session clock is unchanged.
- Playable-path, calendar, Rules and TV-source tests have updated expectations. Broader legacy modal/text-visibility tests still need alignment and execution; compilation alone is not a Play Mode or visual pass.

## 1. Source and context binding

This specification is bound to:

- the confirmed ROOM3 intent on the current integration line;
- the curated ROOM3 asset set, whose visible object locations, five-slot direction lock, and `2014 축제 준비위원회` album identity are approved product inputs;
- [`ROOM2_SPEC.md`](ROOM2_SPEC.md), especially its fixed compact inventory drag/drop, item-family separation, one-time acquisition, refusal, reinspection, and progress-state conventions; and
- the cross-room continuity context in [`ROOM2_ROOM3_IMPLEMENTATION_PLAN.md`](ROOM2_ROOM3_IMPLEMENTATION_PLAN.md) only where it states already-confirmed product behavior rather than Design or implementation shape.

This specification states required player-observable outcomes. It does not approve a class hierarchy, file layout, scene graph, prefab plan, input implementation, animation, font, or test harness.

Throughout this specification:

- **Confirmed** means required by the supplied ROOM3 intent.
- **Integration assumption** means required for cross-room continuity but not yet guaranteed by a concrete shared-system contract.
- **Design/TBD** marks an unresolved presentation or integration choice that implementation must not silently decide in the Spec layer.

## 2. Purpose, success terminal, and non-goals

### Purpose

Define the bounded ROOM3 living-room experience as two distinct cabinet-related stages followed by the existing album/photo/date exit chain:

1. collect and place `L`, `B`, `U`, and `M` on the central room letter board, where `A` starts fixed and the completed board visibly yields the clue word `ALBUM`;
2. use that clue on a separate five-position English combination lock on the china cabinet;
3. after the cabinet unlocks, discover the activity-album cut-out, combine the two actual old-photo pieces in inventory, place the combined old photo into the cut-out, inspect the image-based gaze clue, set the direction drawer lock, acquire the interview notice/date clue, set the calendar to `2015 / 05 / 20`, and leave for ROOM4;
4. support the independent tissue/mirror cleaning branch without making it an exit gate.

### Player-visible success terminal

ROOM3 succeeds when the wall calendar is set to `2015 / 05 / 20`, the exit door opens automatically without a separate password submission, and the open exit permits transition to ROOM4. Setting `2015 / 05 / 17` or any other date must not open the door. The student interview notice is scoped only to one-time acquisition into ROOM3 inventory and ROOM3 reinspection; ROOM3 exit is not gated by any future notice use, and later-room semantics remain outside this Spec.

### Explicit non-goals

This specification does not:

- implement code or prescribe Unity scenes, scripts, prefabs, hierarchy, classes, packages, files, or APIs beyond the approved player-visible asset facts;
- prescribe exact pointer gestures, source-object move/open gestures, arrow widgets, hit coordinates, snap behavior, font assets, visual effects, sound, animations, or control layout;
- choose the exact decoy letters, candidate ordering, initial combination, or art for the china-cabinet lock, except for the observable constraints below;
- redesign ROOM1, ROOM2, or their owned/shared systems;
- permit the old-photo pieces to combine before the activity album/event page and empty cut-out area have been discovered;
- treat the ROOM2 sink photos `X/Y/Z/W` as old-photo pieces or ROOM3 album inputs;
- describe the school album as an official graduation album;
- identify an attacker or establish more about the attack than the confirmed mirror evidence supports;
- make the mirror story discovery a prerequisite for the calendar, exit, or ROOM4 transition;
- impose one total order on independent branches beyond actual puzzle prerequisites;
- add a timer rule, penalty, required hint or advertisement/rewarded-ad assistance, alternate exit condition, or additional cabinet contents for this slice.

## 3. Preconditions and carried-item assumptions

### Confirmed preconditions and continuity invariant

- ROOM3 is a living room.
- ROOM1, ROOM2, and ROOM3 form a mechanically connected item-continuity chain. ROOM4 has narrative continuity, but this Spec does not require, specify, or reconcile any ROOM4 use/carry prerequisite for the ROOM3 student interview notice.
- Completing ROOM3's activity-album photo puzzle requires two actual, distinct old-photo items:
  - the ROOM1 left/first piece, depicting or indicating four people;
  - the ROOM2 right/second piece, depicting or indicating one person and still blurry.
- The pieces have not been combined, completed, or clarified in ROOM2.
- In ROOM3, the two pieces remain separate and cannot be combined until the unlocked activity album/event page and empty cut-out photo area have first been discovered.
- The player must not be stranded in ROOM2 or ROOM3 without recoverable access to either required old-photo piece. The owner-approved policy may either **(A)** gate the relevant room transition until its required prior-room items are acquired or **(B)** permit revisit/backtracking so the actual missing items can be recovered. This specification does not require both policies and does not choose between them.
- A missing old-photo piece must not be fabricated, directly granted, spawned into inventory, or auto-completed as recovery. ROOM2 consumes its USB after successful computer use, so USB ownership is not a ROOM3 precondition.

### Established ROOM2 product conventions applied to ROOM3

ROOM3 must preserve these conventions where applicable:

- gameplay progress facts are represented separately from presentation sprites or current visual assets;
- stateful/pickup objects are independent from stable background art;
- item-to-target use uses the proven fixed-inventory drag/drop grammar rather than adding a second selected-item normal path;
- one-time acquisitions do not duplicate on repeat inspection;
- refused attempts are observable and do not silently succeed;
- clues and progressed surfaces remain reinspectable unless their confirmed terminal presentation intentionally retires interaction, as with the completed letter board and cleaned mirror;
- item families remain separate and cannot substitute for each other;
- presentation is replaceable without changing puzzle meaning;
- the supplied ROOM3 art fixes the visible letter-source mapping, five-slot direction lock, and `2014 축제 준비위원회` album identity;
- missing final art must not block functional implementation: render a clearly localized placeholder first, record the missing-asset request, and replace it later without changing Controller state;
- player input must not leak hidden answers, internal state names, or future clue results.

### Integration assumptions and open continuity choices

- **Integration assumption:** once acquired, the ROOM1 and ROOM2 old-photo pieces survive mechanically connected progression as distinct, usable items without duplication; if either is not yet acquired, the chosen policy preserves recovery access.
- **Integration assumption:** ROOM3 can use the shared acquisition, fixed-inventory drag/drop, inspection, and item-target refusal semantics proven in ROOM2.
- **Integration assumption:** if a shared timer exists, ROOM3 integration does not accidentally reset, duplicate, or stop it. No ROOM3-specific timer behavior is confirmed.
- **Design/TBD:** the exact shared inventory, cross-scene handoff, save/load, and persistence lifetime contract.

## 4. Observable flow and dependency graph

Required dependencies are:

```text
Find L + B + U + M in any order
        ↓
Place letters on the central room letter board in strict order L → B → U → M
(A starts fixed; only the current expected owned letter is accepted)
        ↓
Completed board visibly yields clue word ALBUM
        ↓
Set separate china-cabinet English combination lock to A/L/B/U/M
        ↓
Cabinet unlocks → inspect/open activity album and discover empty cut-out photo area
        ↓
Combine ROOM1 left piece + ROOM2 right piece in inventory into one combined old photo
        ↓
Drag combined old photo onto the album cut-out area
        ↓
Completed clear five-person photo
        ↓
Inspect the five-person gaze arrangement in the photo itself from left to right; no text overlay prints the direction answer
        ↓
Set five-position direction drawer lock to ↑ / ↓ / ← / ↓ / →
        ↓
Acquire/inspect 학생 면담 통지서 → actual interview date 2015-05-20
        ↓
Set calendar to 2015 / 05 / 20
        ↓
Exit opens automatically → ROOM4 transition
```

A separate branch is independently completable:

```text
Collect clean tissue → use it on obscured mirror → switch to the cleaned-mirror image and retire mirror interaction
```

Letter items may be collected in any order, but board placement order is strict. Completing the board yields the clue `ALBUM`; it does not itself unlock the china cabinet. The cabinet unlocks only through the separate five-position combination lock. The tissue/mirror branch may occur whenever its own prerequisite is met and is non-gating. Objects such as the letter board, combination lock, direction lock, mirror, calendar, and exit may be inspected or attempted early; they advance only when their contracts are satisfied.

## 5. Object and item catalog

| Object/item | Initial/source state | Required observable role and persistence | Acquisition / use status |
|---|---|---|---|
| Central room letter board `A` | `A` starts fixed on the board | Shows the start of the clue word; accepts later letters only in expected order; completed board visibly reads `ALBUM` and retires its interaction/drop hotspot | Fixed room state |
| Sofa cushion | Initially covers the `U` item and its hotspot | Moving it slightly changes and persists the cushion presentation, immediately revealing a separate `U` item layer/hotspot | Stateful room object; exact move gesture is Design-owned |
| Coffee-table magazine | Initially covers the `M` item and its hotspot | Moving it upward changes and persists the magazine presentation, reveals a separate `M` item layer/hotspot, and avoids covering the central letter board | Stateful room object |
| Picture frame | Initially covers/obscures the `L` item and its hotspot | Moving/offsetting it changes and persists the frame presentation, immediately revealing a separate `L` item layer/hotspot | Stateful room object; exact move gesture is Design-owned |
| Ordinary TV-cabinet compartment/object | Initially covers/contains the `B` item and its hotspot; this is not the separately locked china cabinet | Opening or moving it changes and persists its presentation, immediately revealing a separate `B` item layer/hotspot | Stateful room object; exact open/move gesture is Design-owned |
| Letter `L` | Initially hidden behind the unshifted picture frame | After the frame is moved, appears as a separate collectible item/layer; accepted only as the first placed movable board letter | One-time acquisition; acquiring it removes only the `L` item/layer; source object remains moved; accepted board placement later fixes it to board state and it is no longer carried/draggable |
| Letter `B` | Initially hidden inside the closed ordinary TV-cabinet compartment/object | After the compartment is opened, appears as a separate collectible item/layer; accepted only after `L` is fixed | One-time acquisition; acquiring it removes only the `B` item/layer; source object remains open; accepted board placement later fixes it to board state and it is no longer carried/draggable |
| Letter `U` | Initially hidden beneath the unmoved sofa cushion | After the cushion is moved, appears as a separate collectible item/layer; accepted only after `L` and `B` are fixed | One-time acquisition; acquiring it removes only the `U` item/layer; source object remains moved; accepted board placement later fixes it to board state and it is no longer carried/draggable |
| Letter `M` | Initially hidden beneath the unmoved coffee-table magazine | After the magazine is moved, appears as a separate collectible item/layer; accepted only after `L`, `B`, and `U` are fixed | One-time acquisition; acquiring it removes only the `M` item/layer; source object remains moved; accepted board placement later fixes it to board state and it is no longer carried/draggable |
| China-cabinet English combination lock | Separate five-position adjustable/cycling lock; initial value is not `ALBUM` | Auto-unlocks immediately when all five positions match `A/L/B/U/M`; any other value remains locked and adjustable | Room object; positions remain adjustable until solved, then the lock target/control is retired as inactive |
| Locked china cabinet | Initially locked behind the combination lock | Opens only after the separate lock is set to `ALBUM`; remains unlocked afterward | Room object |
| `2014 축제 준비위원회` activity album | Inside the unlocked cabinet | The only selectable/investigable cabinet content in this slice; the event page dated `2014.09.21` and its cut-out remain reinspectable | Retained accessible clue; inventory status not required |
| ROOM1 left old-photo piece | Carried distinct item; four people | Remains separate until the activity album/event page and empty cut-out area are discovered; then can combine only with the ROOM2 right piece | Cross-room retained item; replaced by the combined old photo only on valid post-discovery combination |
| ROOM2 right old-photo piece | Carried distinct blurry item; one person | Remains separate until the activity album/event page and empty cut-out area are discovered; then can combine only with the ROOM1 left piece | Cross-room retained item; replaced by the combined old photo only on valid post-discovery combination |
| Combined old photo | Created only by combining the ROOM1 left piece and ROOM2 right piece after cut-out discovery | Draggable inventory item accepted by the album cut-out area; wrong targets preserve ownership | One-time combined item; removed from carried inventory only after successful cut-out placement |
| Completed group photo | Produced only when the combined old photo is placed into the discovered album cut-out | Clear five-person committee photo; all faces identifiable; remains closely inspectable | Persistent album clue; exact representation Design/TBD |
| Directional drawer lock | Separate locked drawer with five adjustable/cycling direction positions | When the five positions align as `↑ / ↓ / ← / ↓ / →`, shows `풀렸다` for one second, retires the controls, clears the message, and returns to the center-wall view; the drawer remains closed until its region is clicked | Room object |
| `학생 면담 통지서` | Inside directional drawer | Clicking the unlocked drawer region opens the drawer closeup. `확인하기` reads the notice without acquiring it; `획득하기` adds it without auto-opening it. After acquisition, it is reinspected only through its inventory card | Retained ROOM3 inventory clue; no ROOM4 use/carry prerequisite is specified |
| Clean tissue | On coffee table | Collectible once and applicable to the mirror | One-time acquisition; consumed only on successful mirror cleaning while cleaned state persists |
| Mirror | Initially obscured by dust/stains | Successful tissue use switches to the supplied cleaned-mirror image and retires the mirror cleaning/inspection hotspot; Back remains available | Room object; transformed non-interactive state |
| Eight-digit numeric calendar | Wall-mounted | Seven-segment year/month/day control constrained to real dates from 2014 through 2016; `▲` increases and `▼` decreases a digit | Room object; input may be retried until solved |
| Exit door and formula | Door initially closed; `yyyy + mm + dd` displayed above it | Opens automatically only at the actual interview date; permits ROOM4 transition | Persistent room progress |

**Separation rule:** ROOM2's sink photos `X/Y/Z/W` are a separate code-clue set. They are not accepted by the old-photo combine action, activity-album cut-out area, central letter board, or china-cabinet lock and must never be conflated with either old-photo piece, the combined old photo, or the completed group photo.

## 6. Puzzle contracts

### 6.1 Central `ALBUM` letter board

- The board is visible in the room and begins with `A` fixed.
- The player can acquire distinct `L`, `B`, `U`, and `M` letter items at the specified locations.
- Each movable letter uses the same stateful-container/separate-content pattern while preserving its confirmed location:
  - `L` is revealed by moving/offsetting the picture frame;
  - `B` is revealed by opening or moving its ordinary TV-cabinet compartment/object, not by unlocking the separately locked china cabinet;
  - `U` is revealed by moving the sofa cushion;
  - `M` is revealed by moving the coffee-table magazine.
- Each source object has a persistent moved/open state. Its one-shot source hotspot retires immediately after that transition; only the newly revealed letter hotspot remains until acquisition.
- Before its source object is moved/opened, the corresponding letter item and pickup hotspot remain hidden.
- Moving/opening the source object changes and persists that source object's presentation and immediately reveals a separate letter Sprite/layer/hotspot.
- Revealing a letter never auto-acquires it; the player separately confirms/acquires the revealed letter.
- Acquiring a revealed letter removes only that letter Sprite/layer/hotspot; the source object remains moved/open.
- Source-object moved/open facts, letter-revealed facts, and letter-owned facts are determined by room/controller state, not by reading the background Sprite or current art asset.
- Exact source-object move/open gestures and art implementation are Design concerns.
- The coffee-table puzzle image is the stable base surface. Clicking or dropping on the board does not navigate to or swap in a second background; tissue, magazine, revealed `M`, and board progress are independently composited over that base.
- The board uses the fixed compact inventory drag/drop grammar for item-to-target use directly on the coffee-table surface.
- The board's expected movable-letter order is strictly `L → B → U → M`.
- Only the current expected letter is accepted, and only if the player owns that distinct letter item.
- A wrong, out-of-order, already-fixed, unowned, or unrelated item is refused without loss, duplication, or progress rollback.
- Each accepted letter becomes fixed board state and is no longer carried or draggable as a reusable item.
- Completing the board visibly yields the clue word `ALBUM`; the completed image remains visible while the board interaction/drop hotspot retires.
- Completing the board does not unlock the china cabinet, open the activity album, or bypass the cabinet combination lock.

### 6.2 China-cabinet English combination lock

- The china cabinet is protected by a separate five-position English combination lock that reads as a rotating/cycling letter lock.
- Each of the five positions is independently adjustable/cyclable.
- Each position draws from the same deliberately limited candidate pool: the five solution letters `A`, `L`, `B`, `U`, and `M`, plus exactly three or four decoy letters, for eight or nine total candidates.
- The candidate pool is not the full alphabet.
- The exact decoy identities, candidate order, initial non-solution value, gesture/control widgets, font, snap behavior, and animation are Design/TBD.
- The initial lock value must not already be `ALBUM`.
- As soon as the full five-position arrangement matches `A/L/B/U/M`, the lock auto-unlocks immediately and releases the cabinet; no separate submit or confirm action is required.
- Successful unlock displays `풀렸다` on the solved lock for one second with its controls inactive, then clears the message and automatically returns to the center-wall view.
- After successful unlock, the lock is retired as an interaction target: its adjustment hotspot/control becomes inactive and cannot receive further input.
- The opened china cabinet is visible on the returned center-wall view without additional automatic hotspot emphasis; its activity-album hotspot becomes available.
- The retired lock may disappear or remain as a non-interactive unlocked visual per Design.
- Any other combination leaves the cabinet locked and adjustable without false-success feedback or progress loss.
- Once unlocked, the cabinet remains unlocked and repeat lock manipulation is impossible; repeat cabinet interaction does not relock it or duplicate unlock progress.

### 6.3 Cabinet album identity and removed-photo discovery

- In this slice, the unlocked cabinet exposes no selectable/investigable content other than the school-related `2014 축제 준비위원회` activity album.
- It is a school-event activity record, not an official graduation album.
- Opening it exposes the `2014 축제 준비위원회` event page dated `2014.09.21`.
- The group-photo area on that page is empty because the photo was deliberately cut out. One image-aligned photo-frame region owns click investigation and valid combined-photo drag/drop; these are not separate competing hotspot regions.
- After placement, the same photo-frame region owns completed-photo inspection. The album and its information remain available for later reinspection.

### 6.4 Old-photo discovery, inventory combination, and album placement

- Opening the event page exposes an empty cut-out photo area.
- Discovering the unlocked activity album/event page and its empty cut-out photo area is the observable trigger that enables inventory combination for the old-photo pieces.
- Before that cut-out discovery, the ROOM1 left old-photo piece and ROOM2 right old-photo piece remain separate and cannot be combined prematurely.
- After cut-out discovery, exactly the ROOM1 left old-photo piece plus the ROOM2 right old-photo piece can combine in inventory into one combined old-photo item.
- A valid combination removes/replaces the two separate old-photo pieces with the combined old-photo inventory item, clears the old selection, and closes the obsolete input card/detail while leaving the inventory window available.
- ROOM2 sink photos and any unrelated items are rejected by the old-photo combine action without loss, consumption, substitution, or progress.
- The player then drags the combined old photo onto the album cut-out area using fixed-inventory drag/drop item-to-target use.
- Successful placement removes the combined old photo from carried inventory, displays the completed newly clear five-person group photo in the album, and enables reinspection through the same photo-frame region.
- A wrong target or wrong item preserves ownership and does not advance album/photo progress.
- If either actual old-photo piece is missing, the chosen continuity policy preserves a route to recover the actual piece rather than fabricating or directly granting one.
- The completed photo establishes that the four missing people and the previously unidentified fifth person belonged to the same `2014 축제 준비위원회`.

### 6.5 Gaze-direction clue

- Close inspection of the completed five-person group photo lets the player read one cardinal gaze direction for each person from the image, left to right: up, down, left, down, right.
- The resulting drawer-lock arrangement is exactly `↑ ↓ ← ↓ →`.
- No green sentence, glyph sequence, or other text overlay prints that answer after `자세히 본다`; feedback only confirms that the player inspected the figures' gazes.
- The clue is unavailable before the combined old photo has been placed into the album cut-out. The completed photo remains reinspectable.

### 6.6 Directional drawer lock

- The separate drawer starts locked behind the supplied five-position adjustable/cycling direction lock.
- The lock is analogous to the china-cabinet English combination lock, but each position cycles only the four direction candidates: up, right, down, and left.
- Each of the five positions is independently adjustable/cyclable.
- As soon as the full five-position arrangement aligns as up, down, left, down, right (`↑ ↓ ← ↓ →`), the lock auto-unlocks immediately and releases the drawer; no separate submit or confirm action is required.
- Successful unlock displays `풀렸다` on the solved lock for one second with its controls inactive, then clears the message and returns to the center-wall view without opening the drawer.
- After successful unlock, the lock is retired as an interaction target: its adjustment hotspot/control becomes inactive and cannot receive further input.
- Clicking the drawer region on the returned center-wall view explicitly opens the drawer and navigates to its closeup; unlock alone must not show the open-drawer image.
- After the notice is acquired, the closeup uses the empty-drawer image. Closing the drawer returns to the center-wall view, and the empty drawer may be reopened from there.
- Any other arrangement leaves the drawer locked and adjustable without false-success feedback or progress loss.
- Exact pointer gesture, arrow assets/font, snap controls, and feedback presentation are Design concerns.
- Once unlocked, the drawer remains unlocked and repeat lock manipulation is impossible. Opening the drawer exposes the notice for one-time acquisition without duplicating it.

### 6.7 Interview notice

- The opened drawer contains a one-time acquired inventory item, `학생 면담 통지서`.
- Before acquisition, `확인하기` opens the readable notice surface without changing ownership; Back returns to the open-drawer closeup.
- `획득하기` adds the notice to inventory, removes it from the drawer, and leaves the empty-drawer closeup visible without automatically reading it.
- The notice states both:
  - document creation date: `2015-05-17`;
  - actual interview date: `2015-05-20`.
- After acquisition, room hotspots do not reopen the notice. Its inventory card displays the exact name `학생 면담 통지서`, exposes the read/confirm action, and remains reusable without consumption or duplication.
- The actual interview date, not the creation date, is the final calendar clue.
- ROOM3 exit must not be gated by any future notice use; later-room notice semantics remain outside this Spec.

### 6.8 Tissue and mirror story branch

- The clean tissue is collectible from the coffee table.
- Before cleaning, dust/stains prevent identification of the player's face in the mirror.
- Attempting to clean or resolve the mirror without the tissue does not reveal the face or neck evidence.
- Using the clean tissue successfully on the mirror cleans the mirror and consumes the tissue.
- The cleaned-mirror image reveals the player's face and the supplied story evidence.
- On successful cleaning, the image changes immediately and both the cleaning target and mirror inspection hotspot retire. The cleaned image persists as a non-interactive result; only Back remains available.
- No extra `확인한다` or `자세히 본다` step follows cleaning, and repeating the interaction cannot dirty the mirror, require another tissue, or duplicate story progress.
- This branch is story content but does not gate the exit under the current requirements.

### 6.9 Calendar and exit

- The wall-mounted numeric calendar permits eight-digit adjustment of year, month, and day. `▲` increases the selected digit and `▼` decreases it.
- Every retained value must be a real date from `2014-01-01` through `2016-12-31`; impossible month/day combinations are skipped rather than committed.
- The text above the exit reads exactly `yyyy + mm + dd`.
- Setting the calendar to `2015 / 05 / 20` opens the exit automatically. No separate password submission is required.
- On the correct date, the calendar displays `풀렸다` for one second with its controls inactive, then clears the message and automatically returns to the right-wall view.
- Setting it to the creation date `2015 / 05 / 17`, or any other valid date, does not open the exit and allows continued adjustment.
- The player need not complete the mirror branch for the correct date to open the exit. Once open, the exit remains open, permits ROOM4 transition, and repeat interaction does not relock it or trigger duplicate completion.

## 7. Required domain progress facts

ROOM3 must preserve the following gameplay facts for the required play/session continuity. These are domain facts, not class, API, file, serialization, or Sprite requirements:

- availability of each distinct carried old-photo piece at ROOM3 entry;
- moved/open state of each letter source object: sofa cushion, coffee-table magazine, picture frame, and ordinary TV-cabinet compartment/object;
- revealed state of each separate letter item layer/hotspot for `L`, `B`, `U`, and `M`;
- acquisition status of each `L`, `B`, `U`, and `M` letter;
- fixed board status of `A`, `L`, `B`, `U`, and `M`;
- current expected board letter and whether the board clue `ALBUM` is complete/reinspectable;
- current five-position china-cabinet lock value while locked;
- whether the china-cabinet lock target/control has been retired as inactive;
- whether the china cabinet is unlocked;
- whether the activity album and deliberately cut-out event page are available for reinspection;
- whether the activity album/event page and empty cut-out photo area have been discovered;
- whether old-photo inventory combination is enabled by that discovery;
- availability/separate ownership of the left and right old-photo pieces before valid combination;
- whether the combined old-photo inventory item exists;
- whether the combined old photo has been placed into the album cut-out;
- whether the five-person photo is complete/clear and its committee identity and image-based gaze clue are inspectable without a text answer overlay;
- current five-position direction drawer lock arrangement while locked;
- whether the direction-lock target/control has been retired as inactive;
- whether the directional drawer is unlocked and whether it is currently open;
- whether the interview notice is still in the drawer or has been acquired into ROOM3 inventory for inventory-only reinspection;
- whether the tissue has been acquired, whether it has been consumed by successful mirror cleaning, and whether the mirror has changed to its cleaned non-interactive image;
- the calendar's current valid value;
- whether the exit has opened and whether ROOM3 completion/ROOM4 transition has occurred;
- recoverable access to both actual old-photo pieces until valid combination, under the owner-approved transition-gate or revisit policy.

Repeat interactions must not roll these facts backward or duplicate one-time acquisitions.

## 8. Failure and repeat-interaction contracts

Feedback wording and audiovisual presentation are Design concerns unless exact content is specified elsewhere, but refusal must be observable rather than a false success.

| Situation | Required observable outcome |
|---|---|
| Letter source object inspected before movement/opening | Its letter remains hidden; no pickup is granted |
| Sofa cushion, coffee-table magazine, picture frame, or ordinary TV-cabinet compartment/object moved/opened | That source object's moved/open presentation persists and its separate letter Sprite/layer/hotspot is immediately revealed |
| Revealed letter acquired | Only that letter Sprite/layer/hotspot is removed; its source object remains moved/open; no duplicate letter is granted on repeat inspection |
| Board attempted with a letter the player has not acquired | The board state does not change; no absent letter is fabricated |
| Board attempted with the wrong, out-of-order, already-fixed, or unrelated item | The item is not lost or consumed; board progress does not advance; retry remains possible |
| Accepted board letter targeted again | No duplicate letter, duplicate placement, or rollback occurs |
| Board completes as `ALBUM` | The clue word remains visible, its interaction/drop hotspot retires, and the china cabinet remains locked until the separate lock is solved |
| China-cabinet lock value is anything other than `ALBUM` | Cabinet remains locked; the lock remains adjustable/retryable without false-success feedback or progress loss |
| China-cabinet lock initial state is inspected | It is not already set to `ALBUM` |
| China-cabinet lock reaches `ALBUM` | Solved lock displays `풀렸다` for one second with controls inactive, then clears the message, returns to the center-wall view, shows the open cabinet, and retires the lock target/control |
| Retired china-cabinet lock manipulated after unlock | Input is ignored/refused without adjustment, relock, duplicate feedback, or duplicate progress; the unlocked cabinet is the active object |
| Cabinet unlocked and revisited | Cabinet remains unlocked and does not duplicate contents or unlock progress |
| Old-photo combination attempted before activity-album cut-out discovery | The two pieces remain separate; no combined old photo is created; no item is consumed or lost |
| Old-photo combination attempted with only one actual piece available | No combined old photo is created; no counterpart is fabricated or directly granted; the chosen policy either had gated this progression or preserves backtracking to recover the actual missing piece |
| Old-photo combination attempted with a sink photo or unrelated item | Combination is refused; unrelated item is not consumed; old-photo progress does not advance |
| Separate old-photo piece offered directly to the album cut-out | Placement is refused; ownership is preserved; completed photo is not displayed |
| Combined old photo offered to the wrong target | Placement is refused; combined old-photo ownership is preserved; album/photo progress does not advance |
| Combined old photo offered to the discovered album photo-frame region | Combined old photo is removed from carried inventory; completed clear group photo appears; the same image-aligned region becomes its inspection hotspot |
| Completed photo inspected | The photo remains visible and its gaze arrangement can be read from the art; no green sentence or arrow sequence prints the answer |
| Completed photo or album revisited | Album identity, deliberate cut-out, clear group photo, committee relationship, and image-based gaze clue remain inspectable as applicable |
| Direction drawer lock arrangement is anything other than `↑ ↓ ← ↓ →` | Drawer remains locked; the lock remains adjustable/retryable without false-success feedback or progress loss |
| Direction drawer lock reaches `↑ ↓ ← ↓ →` | Solved lock displays `풀렸다` for one second with controls inactive, then clears the message and returns to the center-wall view without opening the drawer |
| Retired direction lock manipulated after unlock | Input is ignored/refused without adjustment, relock, duplicate feedback, or duplicate progress |
| Unlocked drawer region clicked on CenterWall | Drawer explicitly opens into the notice/empty closeup; unlock alone never opens it |
| Directional drawer notice checked before acquisition | `확인하기` opens the readable notice without adding it; Back returns to the open drawer |
| Directional drawer notice acquired | `획득하기` adds it once without auto-reading it; the open drawer becomes empty and its remaining hotspot closes the drawer |
| Acquired notice read from inventory | The same notice and both dates reopen without consumption or duplication; no room hotspot provides a second read path |
| Mirror inspected/used without tissue | Face and neck evidence remain unresolved; mirror does not become clean |
| Tissue used on a wrong target | Tissue remains owned; mirror does not become clean |
| Tissue successfully used on mirror | Tissue is consumed; mirror changes to the cleaned image; all mirror hotspots retire while Back remains available |
| Cleaned mirror revisited or cleaning repeated | Mirror remains clean and non-interactive; no duplicate tissue use or story progress is possible |
| Calendar set to a wrong date | Exit remains closed; calendar remains adjustable; other progress remains intact |
| Calendar set to `2015 / 05 / 17` | Exit remains closed, specifically guarding against the creation-date trap |
| Correct date set after wrong attempts | Calendar displays `풀렸다` for one second with controls inactive, then clears the message and returns to RightWall with the exit open |
| Advertisement/rewarded-ad hint used, if such assistance exists | It provides location-only guidance, such as `ROOM 1의 서랍을 다시 확인해보세요.`; it does not grant, spawn into inventory, fabricate, or auto-complete a USB or photo piece, and viewing it is never required for solving or recovery |
| Open exit used repeatedly | Exit remains open; no duplicate completion/transition side effect is required; no future notice use gates ROOM3 exit |

## 9. Acceptance scenarios and evidence/oracles

The oracle is observed room state, exact visible clue content, distinct item/progress state, and actual transition behavior — not an implementation's self-reported success log alone.

### A. Happy path to ROOM4

**Given** ROOM3 starts with both old-photo pieces carried separately
**When** the player collects `L`, `B`, `U`, and `M`; places them on the central board in order `L → B → U → M`; observes clue word `ALBUM`; sets the separate cabinet lock to `ALBUM`; opens the cabinet and album; discovers the empty cut-out; combines the two old-photo pieces; drags the combined old photo onto the shared album photo region; reads the gaze arrangement from the image; sets the directional drawer lock to `↑ / ↓ / ← / ↓ / →`; returns to CenterWall; clicks the unlocked drawer region; acquires/inspects the notice; and sets the calendar to `2015 / 05 / 20`
**Then** the board clue and hotspot retirement, one-second lock feedback, cabinet unlock, completed photo, drawer unlock without auto-open, explicit drawer opening, notice, one-second calendar feedback, exit opening, and ROOM4 transition occur in that dependency order.

**Evidence/oracle:** direct observation of `A` initially fixed; each letter source object initially hiding its letter; source-object movement/opening persisting while a separate letter Sprite/layer/hotspot appears; separate confirmation/acquisition removing only the letter layer/hotspot; accepted board placement order; board clue `ALBUM`; separate cabinet lock state changing to `ALBUM`; activity-album cut-out discovery before old-photo combination; separate old-photo items replaced by one combined old-photo item; combined item removed only on successful cut-out placement; direction drawer lock state changing to `↑ / ↓ / ← / ↓ / →`; each locked/unlocked state; exact album title, event identity, direction clue, both notice dates, ROOM3 notice inventory/reinspection state, and `yyyy + mm + dd` text; actual open-door navigation/ROOM4 transition after the correct calendar value.

### B. Board order and cabinet separation

**Given** the player owns some or all of `L`, `B`, `U`, and `M`
**When** the player attempts to place a non-current letter, an unowned letter, an already-fixed letter, or an unrelated item on the central board
**Then** placement is refused without item loss or progress rollback.
**When** the letters are placed in strict order `L`, `B`, `U`, `M`
**Then** the board visibly reads `ALBUM`; its hotspot retires; the accepted letters are no longer carried/draggable; and the china cabinet remains locked until the separate lock is set correctly.

**Evidence/oracle:** before/after board state and inventory ownership for wrong and correct attempts; visible completed board clue; cabinet still locked immediately after board completion.

### C. China-cabinet lock candidate pool and retry

**Given** the central board clue is known or unknown
**When** the china-cabinet lock is inspected
**Then** it has five independently adjustable/cycling positions, all using the same limited pool of eight or nine candidates: `A`, `L`, `B`, `U`, `M`, plus exactly three or four decoys; it is not already `ALBUM`; and it is not a full-alphabet lock.
**When** any non-`ALBUM` combination is set
**Then** the cabinet remains locked and adjustable without false-success feedback or progress loss.
**When** the full arrangement becomes `ALBUM`
**Then** the cabinet unlocks without a separate submit/confirm action, displays `풀렸다` for one second, returns to CenterWall with the message cleared, retires the lock target/control, and exposes the opened cabinet and album.

**Evidence/oracle:** visible/captured candidate cycle for each position; initial value capture; locked state after a wrong combination; timed solved-lock feedback and automatic CenterWall return when the arrangement reaches `ALBUM`; failed/ignored post-unlock manipulation; successful album interaction.

### D. Cross-room old-photo continuity, discovery-gated combination, and item-set separation

**Given** the player obtained the left piece in ROOM1 and the blurry right piece in ROOM2 without ROOM2 combining them
**When** the player reaches ROOM3 before discovering the unlocked activity album/event page and empty cut-out area
**Then** the two pieces remain separate and cannot be combined prematurely.
**When** the cut-out area has been discovered
**Then** combining exactly the ROOM1 left piece and ROOM2 right piece replaces them with one combined old-photo inventory item; ROOM2 sink photos `X/Y/Z/W` and unrelated items are rejected without loss.
**When** only one actual old-photo piece is available
**Then** combination and clarification do not occur, no counterpart is fabricated or directly granted, and the selected gate/backtracking policy leaves the actual counterpart recoverable.
**When** the combined old photo is dragged onto the same image-aligned album region used to inspect the empty photo frame
**Then** the combined item is removed from carried inventory, its obsolete input card/detail is closed, and one clear, identifiable five-person group photo appears with that same region now serving inspection.

**Evidence/oracle:** inventory/state identity across both transitions; failed pre-discovery combine attempt; cut-out discovery observation; before/after inventory state for valid combination; refusal observation using a sink photo without inventory loss; cut-out placement observation that removes only the combined item and reveals the completed photo.

### E. Album identity and intentional-removal discovery

**Given** the china cabinet has unlocked through the separate English combination lock
**When** the cabinet and school album are investigated
**Then** the only selectable/investigable cabinet content for this slice is the `2014 축제 준비위원회` activity album, its event page dated `2014.09.21` has an empty group-photo area, and the player can learn that the area was deliberately cut out.

**Evidence/oracle:** visible album title/page content and empty cut-out presentation; interaction sweep confirming no other cabinet content is selectable/investigable in this slice.

### F. Gaze clue and direction-lock refusal

**Given** the combined old photo has not been placed into the album cut-out
**When** the player inspects the area
**Then** no completed-photo gaze solution is presented.
**Given** the completed photo exists
**When** it is closely inspected
**Then** the player can read up/down/left/down/right from the five figures' gazes in the art, while no green hint sentence or arrow-sequence text exposes the answer.
**When** the five-position direction lock is aligned to any arrangement other than `↑ / ↓ / ← / ↓ / →`
**Then** the drawer stays locked and permits continued adjustment without false-success feedback or progress loss.
**When** the full arrangement becomes `↑ / ↓ / ← / ↓ / →`
**Then** the drawer unlocks without a separate submit/confirm action, displays `풀렸다` for one second, clears the message, returns to CenterWall without opening, and retires the lock controls.
**When** the player then clicks the drawer region
**Then** the drawer opens to its closeup and exposes the notice.

**Evidence/oracle:** direct comparison of incomplete and complete photo states without a textual direction answer; visible/captured direction candidate cycling; locked state after wrong arrangements; timed solved-lock feedback and CenterWall return at `↑ / ↓ / ← / ↓ / →`; failed post-unlock manipulation; explicit drawer-region click before the open-drawer image appears.

### G. Notice reinspection and creation-date trap

**Given** the direction drawer is unlocked and explicitly opened
**When** `확인하기` is chosen
**Then** the notice is readable without acquisition and Back returns to the drawer.
**When** `획득하기` is chosen
**Then** it is acquired once without automatic reading, the drawer becomes empty, and later reinspection is available only from the inventory card; `2015-05-17` remains identifiable as creation date and `2015-05-20` as actual interview date.
**When** the calendar is set to `2015 / 05 / 17` or another incorrect date
**Then** the exit remains closed and adjustment remains possible.
**When** it is changed to `2015 / 05 / 20`
**Then** the exit opens automatically without separate submission.

**Evidence/oracle:** repeated notice captures with labels and dates; actual closed/open exit boundary at each calendar value.

### H. Mirror branch, refusal, repeat behavior, and non-gating status

**Given** the mirror is dirty and the tissue is unavailable
**When** the player inspects or attempts to clean it
**Then** the face and neck evidence are not identifiable.
**When** the tissue is collected and successfully used on the mirror
**Then** the tissue is consumed, the cleaned-mirror image replaces the dirty state, all mirror hotspots retire, and only Back remains available; no additional mirror inspection step or duplicate progress is possible.
**And given** this branch is not completed
**When** the player satisfies the date branch and sets `2015 / 05 / 20`
**Then** the exit still opens.

**Evidence/oracle:** dirty/clean mirror image comparison; tissue ownership before and after wrong-target and successful mirror use; absence of mirror hotspots after cleaning; retained cleaned state; successful exit in a run where tissue/mirror progress remains incomplete.

### I. Reinspection and one-time stability

**Given** letters, tissue, photo pieces, album clues, completed photo, notice, mirror discovery, letter source-object movement/opening, or unlocks have been obtained
**When** their source objects and clue surfaces are revisited after intervening actions
**Then** one-time items are not duplicated, moved/open source objects do not revert, progressed locks do not revert, the completed photo and inventory notice remain available through their approved paths, and the cleaned mirror remains visible but non-interactive.

**Evidence/oracle:** item counts/identities and progress facts before and after repetition; repeated visible clue content; persistent board, cabinet, drawer, mirror, and exit states.

### J. ROOM1, ROOM2, and shared-system non-regression

**Given** ROOM3 is integrated with shared inventory, timer, acquisition/use, and transitions
**When** existing ROOM1 and ROOM2 acceptance/smoke coverage and end-to-end ROOM1→ROOM2→ROOM3 continuity checks run, including the ROOM1 phone-gallery exit path, ROOM2 USB consumption, and attempts with the left or right old-photo piece missing
**Then** their existing puzzle behavior still passes; ROOM2's keypad still accepts `3412` without requiring its old-photo branch; the chosen single gate/backtracking policy prevents an unrecoverable state for every actual required item; no item is fabricated or directly granted; the two old-photo pieces are separately available on ROOM3 entry, combine only after activity-album cut-out discovery, and are replaced by one combined old-photo item; sink photos remain separate; the student interview notice is scoped to ROOM3 inventory acquisition/reinspection only; shared item interactions continue to work under the owner-approved contract; and the timer follows its owner-approved continuity behavior.

**Evidence/oracle:** existing room tests remain passing, supplemented by owner-approved shared-system checks and an observed cross-room run. ROOM3-only success logs are insufficient evidence.

## 10. Open questions and unresolved forks

1. What is the final cross-scene handoff and persistence lifetime for room progress and carried items beyond the current session?
2. What is the owner-approved timer behavior across ROOM2→ROOM3→ROOM4 transitions, if a shared timer is present?
3. Which owner-approved continuity policy applies at each mechanically connected boundary: **(A)** block commitment until required prior-room items are acquired or **(B)** preserve revisit/backtracking to recover the actual items? Advertisement/rewarded-ad hints, if present, remain optional location-only assistance and are not a third recovery policy.
4. Which exact decoy letters are used in the china-cabinet lock's shared candidate pool: three decoys for eight total candidates or four decoys for nine total candidates?
5. What is the candidate order and initial non-`ALBUM` value for the cabinet lock?
6. What exact ordinary TV-cabinet compartment gesture presents the asset-confirmed `B` reveal? This is separate from the locked china cabinet and does not imply an additional puzzle.
7. Which final open-exit and transparent tissue assets will replace the remaining fallbacks, and which rough-rendered surfaces require later presentation optimization?

## 11. Design fence

Later Design owns:

- class/module layout and naming;
- package/file layout;
- Unity scene hierarchy, GameObjects, components, prefabs, and asset selection;
- concrete shared inventory, acquisition/use, timer, transition, and persistence APIs;
- serialization, save/load, cross-scene persistence, and data ownership;
- exact pointer gesture, letter source-object move/open gestures, arrow controls, cycling controls, hit targets, hotspot coordinates, snap implementation, input handling, and control layout;
- exact china-cabinet decoy identities, candidate order, initial non-solution value, font, and optional audio/animation around the fixed one-second solved feedback and CenterWall return;
- exact direction-lock pointer gesture, runtime glyph/font, snap controls, candidate cycling presentation, and optional audio/animation around the fixed one-second solved feedback and CenterWall return;
- visual, animation, audio, camera, lighting, and feedback effects;
- temporary localized rendering for missing assets during functional implementation, plus later replacement with requested final clue, mirror, notice, exit, tissue, animation, and feedback artwork;
- test harness structure and automation details.

Design and implementation must preserve the observable contracts, dependency boundaries, refusals, non-gating mirror branch, cross-room item separation, established ROOM2 product conventions, approved asset-aligned facts, and explicitly open integration questions above.
