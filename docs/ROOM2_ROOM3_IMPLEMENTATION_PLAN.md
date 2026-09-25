# ROOM2–ROOM3 Implementation Plan

**Status:** Historical execution plan for ROOM2. Its ROOM3 details are superseded by the approved [`ROOM3_SPEC.md`](ROOM3_SPEC.md) and [`ROOM3_DESIGN.md`](ROOM3_DESIGN.md).

**ROOM3 authority rule:** do not implement ROOM3 puzzle values, source locations, asset bindings, or phase order from this document. Use `ROOM3_SPEC.md` for product behavior and `ROOM3_DESIGN.md` for implementation. In particular, the approved asset-aligned flow uses `L=액자`, `B=TV장`, `U=쿠션`, `M=잡지`, a five-position direction lock `↑ ↓ ← ↓ →`, and the `2014 축제 준비위원회` album. Missing final art is rough-rendered first and requested after the functional pass.

## 1. Bound inputs

Implementation must follow:

- [`ROOM2_SPEC.md`](ROOM2_SPEC.md)
- [`ROOM3_SPEC.md`](ROOM3_SPEC.md) and [`ROOM3_DESIGN.md`](ROOM3_DESIGN.md), which jointly supersede this document's ROOM3 sections
- imported ROOM1 baseline `2e39382` (`inventory-implementation`), merged into `room2-minju` at `f9bb031`
- any later ROOM1/shared-system changes fetched before a shared-file edit or final merge

If a plan step conflicts with the final ROOM1 code, preserve the Specs' observable behavior and revise this Plan before coding. Do not silently fork the shared system.

## 2. Implementation target sequence

Finish the confirmed ROOM2 interaction refinement and consolidated QA before starting ROOM3. Then deliver the ROOM3 placeholder flow against the stable ROOM2 continuity boundary. Across the two stages, provide:

- confirmed item acquisition and use conditions;
- confirmed puzzle answers and refusal behavior;
- clue reopening/reinspection;
- one-time progress and duplicate prevention;
- ROOM1→ROOM2→ROOM3 item continuity;
- ROOM2 and ROOM3 completion states;
- automated rule tests and Unity smoke coverage;
- replaceable placeholder UI and content slots;
- ROOM2 object-local drag targets, immediate state refresh, numbered photo presentation, and the reusable image-based enlarger workspace confirmed in `ROOM2_SPEC.md`.

The first pass does **not** need final backgrounds, animations, sound, effects, rewarded-ad integration, save/load, or ROOM4 implementation. ROOM2 does require a recognizable independent hook object or explicit asset placeholder, discoverable object-local drag targets, and replaceable Sprite slots for the enlarger base and four result images. Selected-item callbacks remain test-only and are not a player fallback.

## 3. Current repository baseline

The imported ROOM1 branch establishes these usable conventions:

- `Room1GameController` owns ROOM1 progress and coordinates UI/inventory.
- `IRoom1View` allows `Room1PrototypeUI` and `Room1ImageNavigationUI` to satisfy the same ROOM1 View interface.
- `Room1ImageLibrary` stores replaceable ROOM1 sprites in a `ScriptableObject`.
- `InventoryManager` persists with `DontDestroyOnLoad`, provides `AddItem`, `HasItem`, `ItemCount`, and item events, and builds its own runtime uGUI.
- `InventoryItemType` now includes `Phone`, `USB`, `Newspaper`, and `PhotoFragment` in addition to the original newspaper-combination items.
- selecting an inventory slot and activating the same slot emits `ItemUsed`; the selected item is still private and there is no general item-on-room-target interface.
- ROOM1's newspaper combination remains hardcoded inside `InventoryManager`.
- `Room1BatchSmokeTest` covers phone, USB, newspaper, door unlock, and room clear.

Known gaps to address or preserve explicitly:

- `Room1ImageNavigationUI.InspectPhotoFragment` currently shows text but does not add `PhotoFragment` to inventory.
- ROOM1 exit checks phone, USB, and newspaper, but not the required left old-photo piece.
- inventory persists, but no actual ROOM1→ROOM2 or ROOM2→ROOM3 scene transition exists.
- no shared timer, target-use, save/load, or room-progress persistence contract exists; the now-confirmed ROOM2 USB consumption requires a minimal shared item-removal seam.
- the current build settings still do not establish ROOM2/ROOM3 scenes.

Run the existing ROOM1 smoke test before modifying shared code and record the result.

## 4. Collaboration and merge guardrails

ROOM1 remains another contributor's work surface. Preserve it by default:

- prefer new ROOM2/ROOM3 files over edits to ROOM1 files;
- keep `InventoryManager` edits additive and limited to the shared interface ROOM2/ROOM3 actually require;
- do not restructure `Room1ImageNavigationUI`, `Room1PrototypeUI`, or ROOM1 assets;
- fix the ROOM1 photo-fragment acquisition/exit gap in a separate commit from inventory changes;
- before each shared-file commit, fetch the contributor branch and review overlap;
- retain existing ROOM1 public behavior and smoke coverage;
- resolve future merge conflicts by preserving both the contributor's latest ROOM1 behavior and the Specs' cross-room item contract, not by taking an entire side wholesale.

## 5. Design direction

Use three modules per room:

```text
RoomNRules          pure puzzle state and transition results
RoomNGameController Unity/shared-system coordination
RoomNPrototypeUI    placeholder presentation and input forwarding
```

### Rules module interface

The Rules module must hide puzzle state transitions behind a small interface. It must not know about:

- `MonoBehaviour`, scenes, GameObjects, Sprite, Canvas, or input devices;
- inventory UI or item dragging;
- animations, audio, or final images;
- persistence implementation.

Tests call the same Rules interface used by the controller.

### Controller module interface

The controller:

- translates shared inventory/room interactions into Rules requests;
- applies successful results to inventory and View state;
- converts refusal results into player feedback;
- owns no visual layout;
- does not reimplement puzzle rules already held by Rules.

### View module interface

The View:

- displays current room state and confirmed clue text;
- forwards clicks, text/keypad input, and placeholder item-target actions;
- does not decide whether a puzzle succeeds;
- keeps images and hotspot/layout data replaceable.

Do not copy `Room1PrototypeUI` or `Room1ImageNavigationUI` into two monolithic files and then modify them independently. After ROOM1 item/inventory UX passes its integration test, execute Step 6.1 in [`ROOM1_ITEM_INVENTORY_UX_PLAN.md`](ROOM1_ITEM_INVENTORY_UX_PLAN.md): extract only interaction mechanisms with real ROOM2 consumers, then reuse those modules from ROOM2. Keep room puzzle meaning in room-specific Rules/Controllers and avoid a universal room-object base class.

## 6. Ambiguity policy

Implement confirmed behavior; reserve a named integration point for unresolved behavior.

| Unresolved area | First-pass treatment |
|---|---|
| Drag/drop and selected-item use | ROOM2 uses fixed-inventory item drag/drop only. Computer, sink, enlarger object, and enlarger workspace floor expose contextual targets; selected-item-then-click is test-only, not a normal player path. |
| Item consumption after use | Successful computer use removes the USB after memo access is recorded and disables the completed USB target. The hook and numbered sink photos remain owned; mark their room progress separately. |
| Transition gate vs prior-room backtracking | Implement a transition-eligibility check and missing-item result. Do not build backtracking or commit to irreversible transition until the owner chooses the policy. Never fabricate missing items. |
| Timer | Display/use the shared timer only if ROOM1 provides a stable contract. Do not create a second timer. |
| Save/load and persistence lifetime | Keep Rules state in plain C# values without Unity object references, but do not implement save/load or choose a serialization format without the shared contract. |
| Rewarded-ad hints | No ad SDK or reward flow. Reserve location-only hint text data; never grant an item or complete progress. |
| Final images and hotspots | Use replaceable hook/base/result references and tune only enough for consolidated QA. Missing hook cutout or four enlarger result images remain explicit asset dependencies, not text-dashboard substitutes. |
| ROOM3 mirror requirement for the later ending | Record `mirror evidence seen` independently. Keep ROOM3 exit behavior aligned with the current ROOM3 Spec; expose a future transition gate without silently making the mirror mandatory now. |

A placeholder must not report false gameplay success. Developer-only controls may drive Rules or controller smoke tests, but they do not count as the playable user path and must be hidden from normal play. If the final shared item-selection contract is still unavailable, finish Rules/controller verification and stop that UI integration step rather than pretending an item was used.

## 7. Planned file shape

Exact folders may be adjusted to match the final ROOM1 convention, but keep ownership local. Scene files are intentionally absent from this list until Step 0 confirms whether the project uses separate scenes or runtime-created room surfaces:

```text
Assets/Scripts/Room2/Room2Rules.cs
Assets/Scripts/Room2/Room2GameController.cs
Assets/Scripts/Room2/Room2PrototypeUI.cs

Assets/Scripts/Room3/Room3Rules.cs
Assets/Scripts/Room3/Room3GameController.cs
Assets/Scripts/Room3/Room3PrototypeUI.cs

Assets/Tests/EditMode/Room2RulesTests.cs
Assets/Tests/EditMode/Room3RulesTests.cs
Assets/Editor/Room2BatchSmokeTest.cs
Assets/Editor/Room3BatchSmokeTest.cs
```

Potential shared edits must be minimal and additive:

```text
Assets/Scripts/IInventoryAccess.cs       small interface used by controllers and test fakes
Assets/Scripts/InventoryManager.cs       implement selected-item query/clear and add-once behavior
InventoryItemType declaration            add required ROOM2/ROOM3 item identities
Assets/Scripts/Room1GameController.cs    collect/check the left photo piece only
Assets/Scripts/Room1ImageNavigationUI.cs route photo interaction to the controller only
ProjectSettings/EditorBuildSettings.asset only after scene names and transitions are confirmed
```

The inventory seam to confirm through the first red/green slice is intentionally small:

```text
HasItem(type)
AddItemOnce(type, sprite)
TryGetSelectedItem(out type)
ClearSelection()
```

Do not add target names, room rules, USB/computer knowledge, or sink/photo knowledge to this interface. Add only the minimal item-removal operation now required by the approved ROOM2 USB consumption rule. Do not add generic transformation or room-specific consumption logic to the shared inventory.

Do not place ROOM2/ROOM3 puzzle rules inside `InventoryManager`.

## 8. Required item identities

Bind final names to the shared item registration convention after ROOM1 inspection. Required domain identities are:

### Carried into ROOM2

- phone — retained, with its ROOM1-only `사용` action hidden in ROOM2
- USB
- newspaper
- old-photo left piece

### Acquired in ROOM2

- old metal hook
- sink photo 1
- sink photo 2
- sink photo 3
- sink photo 4
- old-photo right piece

Do not expose X/Y/Z/W in the four inventory names. Model visible photo identity (`1`–`4`) separately from the hidden enlarger result (`X`–`W`), even if migration compatibility temporarily maps existing enum values internally.

### Acquired in ROOM3

- letter L
- letter B
- letter U
- letter M
- clean tissue

The ROOM2 sink photos and the old-photo pieces are separate item families. ROOM3's album must reject sink photos.

## 9. ROOM2 Rules slice

### Progress facts

Represent at least:

- USB successfully used / computer memo available;
- hook acquired;
- sink drained;
- four numbered sink photos made available, then acquired together through one sink collection interaction as four distinct item identities;
- reveal/inspection status and result-image binding for photo 1→Z=3, photo 2→W=4, photo 3→X=1, and photo 4→Y=2;
- current enlarger workspace presentation: base floor or the selected photo result;
- right old-photo piece acquired;
- exit keypad unlocked;
- onward-transition eligibility result.

### Confirmed transitions

1. USB unavailable → computer memo remains unavailable.
2. USB used on the active computer target → memo becomes available, the USB is removed, the completed target becomes inactive, and later computer interaction reopens the memo without another USB.
3. Hook collected once → duplicate collection refused.
4. Sink by hand → refusal; sink unchanged.
5. Hook dropped on sink → sink drains once; the same view refreshes immediately, the drain target disables, and the photo hotspot enables.
6. Photos before drain → unavailable.
7. After drain, one sink collection interaction adds the four distinct `사진 1`–`사진 4` items exactly once.
8. Clicking the enlarger opens `확인한다 / 사용한다 / 취소`; inspect shows prose only, while use opens the empty-floor workspace with Back and a live drop area.
9. Dropping any numbered photo directly on the enlarger opens the same workspace on that photo's result. Dropping in the workspace replaces the Sprite and keeps the target active for another photo.
10. Unrelated items are refused without consumption. Each numbered photo remains owned and reinspectable, revealing:
   - 사진 1→Z=3
   - 사진 2→W=4
   - 사진 3→X=1
   - 사진 4→Y=2
11. The right old-photo piece is available from an ordinary ROOM2 drawer with no additional puzzle prerequisite; it is collected once and remains separate and blurry.
12. Wrong keypad code → locked, retry allowed.
13. `3412` → keypad unlocks permanently.
14. Onward progression request checks recoverable access to both old-photo pieces and returns eligible or a specific missing-item result without fabricating anything. Whether that result blocks transition or enables backtracking remains unresolved.

### ROOM2 placeholder View

Build it from stateful room objects rather than full-screen images that encode puzzle state. Reuse the shared field-item presentation, inventory metadata, and item-target drag contracts extracted after ROOM1 integration; do not duplicate ROOM1 modal code.

Provide contextual image-navigation surfaces for:

- computer and a discoverable pre-activation USB drop target; successful use removes the USB and target, shows the computer memo screen, and keeps the memo reopenable through the computer interaction;
- independent hook pickup;
- sink drain target before success and photo hotspot immediately after success, never both as active completion actions;
- four pickups presented as `사진 1`–`사진 4`;
- enlarger object choice (`확인한다 / 사용한다 / 취소`), direct photo-drop shortcut, empty-floor workspace, continuous workspace drop target, Back, and per-photo result-image replacement;
- right-photo drawer and blurry-photo inspection;
- exit keypad;
- message/refusal area.

Developer tests may invoke the same target request directly, but consolidated QA must use actual inventory slots and screen-space drop targets. Remove permanent debug route/action strips and ambiguous placeholder prose from normal play.

## 10. ROOM3 Rules slice

### Progress facts

Represent at least:

- L/B/U/M acquisition and fitted status;
- ALBUM completion and cabinet unlock;
- activity-album inspection availability;
- left/right old-photo placement;
- completed clear five-person photo;
- gaze clue availability;
- directional drawer unlock;
- interview notice availability/reinspection;
- tissue acquisition;
- mirror cleaned;
- face-match/neck-mark evidence seen;
- current calendar year/month/day;
- exit open and ROOM3 completion.

### Confirmed transitions

1. A starts fixed in the ALBUM lock; L/B/U/M may be acquired from their specified locations in any order, once each.
2. Each acquired letter has its corresponding target position. Missing/wrong placement → cabinet remains locked and correction remains possible.
3. ALBUM complete → cabinet remains unlocked.
4. Cabinet exposes only the activity album in this slice.
5. The album exposes distinct left/right placement areas. Either actual piece may be placed first, but each must use its matching area; one placed piece remains incomplete.
6. Both actual old-photo pieces correctly placed → clear five-person photo.
7. Sink photo or unrelated item offered → refused without consumption.
8. Completed photo inspection → gaze sequence available: right, down, left, up.
9. Wrong direction sequence → drawer locked, retry allowed.
10. right/down/left/up → drawer unlocks permanently.
11. Notice exposes both 2015-05-17 and actual interview date 2015-05-20 and remains reopenable.
12. The clean tissue is collected from the coffee table. Mirror use without tissue → refusal.
13. Tissue used → mirror remains clean. Inspecting the clean mirror records that the face/neck evidence was seen; cleaning alone and evidence inspection remain distinguishable facts.
14. Calendar set to 2015-05-17 or another date → exit remains closed.
15. Calendar set to 2015-05-20 → exit opens automatically; ROOM3 completion occurs when the player uses the open exit.
16. Repeated completion interactions do not duplicate items or roll state backward.

### ROOM3 placeholder View

Provide labeled surfaces for:

- L under the sofa cushion, B under the coffee-table magazine, U behind the picture frame, and M at/in the TV cabinet;
- ALBUM cabinet lock with A already fixed and explicit L/B/U/M target positions;
- the sole activity album and two photo-placement slots;
- completed photo and gaze inspection;
- directional input drawer;
- interview notice;
- tissue pickup and dirty/clean mirror;
- adjustable year/month/day calendar;
- exit and message/refusal area.

Do not create final handwriting, mirror, group-photo, calendar, or living-room art.

## 11. Shared-system integration order

### Step 0 — verify the imported ROOM1 baseline

1. Confirm `f9bb031` includes imported ROOM1 commit `2e39382` and record any newer contributor commits.
2. Run ROOM1 smoke coverage.
3. Confirm `IRoom1View`, `Room1ImageLibrary`, persistent inventory, and current item events behave as described in §3.
4. Record the absence of target-use, timer, and scene-transition contracts rather than designing them implicitly.
5. Use the established runtime-created UI/controller convention for placeholder rooms; defer final scene-transition wiring until its policy is approved.

### Step 1 — fix the cross-room ROOM1 item contract

1. Add a controller action that acquires `PhotoFragment` exactly once.
2. Route the image-UI photo hotspot through that action.
3. Require the actual left photo piece, in addition to existing ROOM1 requirements, before ROOM1 clear/progression.
4. Extend ROOM1 smoke coverage for acquisition, duplicate prevention, and missing-piece refusal.
5. Do not add actual scene loading in this step.

### Step 2 — implement the inventory seam test-first

Confirmed test seams:

- `IInventoryAccess` for controller/fake interaction;
- existing ROOM1 public controller behavior for regression;
- no tests against inventory private lists, slot widgets, or pointer internals.

For each method, write one failing behavior test, implement only enough to pass, then continue. Preserve existing `ItemUsed` and newspaper-combination behavior.

### Step 3 — implement pure Rules test-first

1. Add one ROOM2 Rules behavior test and minimal implementation per vertical slice.
2. Add one ROOM3 Rules behavior test and minimal implementation per vertical slice.
3. Verify every confirmed transition and refusal without loading Unity scenes.
4. Keep item IDs as domain inputs/results rather than reaching into inventory globals.

### Step 4 — bind Rules to the shared inventory seam

1. Add the required item identities using the final ROOM1 convention.
2. Prefer additive methods/events over changing existing ROOM1 behavior.
3. Provide only what controllers need now: availability, selected-item query/clear, add-once behavior, and minimal remove behavior for the approved USB consumption rule. Do not add item transformation behavior.
4. Keep newspaper combination behavior passing unchanged.
5. Run ROOM1 smoke tests immediately after this step.

### Step 4.5 — extract shared interaction primitives with the first ROOM2 consumers

1. Complete ROOM1 item/inventory integration and its manual smoke path first.
2. Follow Step 6.1 of [`ROOM1_ITEM_INVENTORY_UX_PLAN.md`](ROOM1_ITEM_INVENTORY_UX_PLAN.md).
3. Reuse the proven field-item choice/presentation and inventory metadata mechanisms for ROOM2 hook/photos/right-piece objects instead of copying ROOM1 View code.
4. Add an item-target contract shared by direct controller tests and ROOM2 drag/drop targets.
5. Keep ROOM1 and ROOM2 puzzle-specific state/results in their own controllers and Rules.
6. Verify ROOM1 behavior before and after extraction.

### Step 5 — finish and refine ROOM2 flow

1. **Bind presentation semantics.** Begin with the four carried ROOM1 items, hide the phone's ROOM1-only `사용` action in ROOM2, consume the USB only after successful computer use, present the four sink photos as `사진 1`–`사진 4`, and remove copy that exposes internal state or future results.
2. **Repair target affordances.** While an item is dragged, highlight the current contextual computer, sink, enlarger-object, or workspace-floor target. An inactive surface or empty space remains a clear refusal without state change.
3. **Repair sink transition.** Use the independent hook object, refresh the same sink view immediately after a successful hook drop, disable the completed drain target, and enable the photo hotspot without requiring navigation away and back.
4. **Refactor enlarger state.** Separate object choice, Controller-owned workspace state (`BaseFloor` or a specific numbered-photo result), and Sprite presentation. Click opens `확인한다 / 사용한다 / 취소`; use opens the empty-floor workspace; direct photo drop opens the corresponding result; workspace drops replace the current result image while retaining Back and the live drop area.
5. **Bind replaceable art.** Add references for the independent hook, hook-removed presentation, enlarged-floor base, and four photo-result Sprites. Keep Controller state independent of whichever Sprite is assigned.
6. **Strengthen automated coverage.** Assert phone action policy by room, target discoverability, same-frame sink refresh, post-drain target disablement, numbered names, both enlarger entry paths, continuous/repeated result drops, wrong-item refusal, item retention, keypad, and onward eligibility. Explicitly prove that the optional old-photo story interaction does not gate keypad acceptance. Keep ROOM1 smoke green.
7. **Run one consolidated ROOM2 QA.** Do not request another partial user test between these substeps. QA the complete player flow and collect remaining copy, coordinate, and art defects as one batch.

### Step 6 — implement ROOM3 placeholder flow

1. Create controller and placeholder View.
2. Bind letters, album slots, tissue, and prior-room old-photo items.
3. Implement gaze, direction lock, notice, mirror state, calendar, and exit.
4. Add ROOM3 batch smoke coverage.
5. Verify ROOM1 and ROOM2 still pass.

### Step 7 — verify room continuity without choosing deferred policy

1. Verify ROOM1 supplies USB and left photo through the real shared contract.
2. Verify ROOM2 preserves the left piece and supplies the right piece.
3. Verify ROOM3 accepts only those two old-photo pieces for album completion.
4. Bind transition eligibility to the owner-selected gate/backtracking policy only after that choice is approved.
5. Confirm missing-item hints identify locations only and never grant items.

### Step 8 — manual Unity validation

Run the acceptance checklist in §13 with placeholder visuals. Record UX issues separately from rule defects.

### Step 9 — later asset pass

After expected images are supplied:

1. replace backgrounds and object images;
2. align hotspots and responsive layout;
3. replace placeholder modals with final clue surfaces;
4. add animations/audio/effects;
5. polish the already-required drag/drop presentation without changing its controller requests;
6. rerun all rule, smoke, and manual flows.

## 12. Commit boundaries

Keep review and merge conflict resolution small:

1. `docs: add room specifications and implementation plan`
2. `fix: carry room 1 photo fragment into later rooms` — include its regression tests
3. `feat: add minimal shared inventory access seam` — include seam tests
4. `feat: add room 2 rule slices` — commit only green vertical slices with their tests
5. `refactor: extract shared room interaction primitives` — only with ROOM1 regression coverage and real ROOM2 consumers
6. `feat: add room 2 placeholder flow` — include drag/drop player path and smoke coverage
7. `feat: add room 3 rule slices` — commit only green vertical slices with their tests
8. `feat: add room 3 placeholder flow` — include its smoke coverage
9. `test: verify room 1 to room 3 continuity`

Do not mix final asset work into these commits.

## 13. Verification checklist

### Automated Rules

- ROOM2 accepts only `3412` for keypad unlock.
- ROOM2 memo and each X/Y/Z/W mapping remain reopenable.
- sink cannot drain by hand or without the hook.
- hook/photos/right piece cannot duplicate.
- ROOM3 cabinet requires ALBUM.
- album requires the two correct old-photo pieces and rejects sink photos.
- direction drawer accepts only right/down/left/up.
- calendar opens the exit only at 2015-05-20, not 2015-05-17.
- repeated interactions do not roll state backward.

### Automated integration/smoke

- existing ROOM1 smoke remains passing.
- ROOM1 items remain available on ROOM2 entry under the chosen continuity policy.
- ROOM2 old-photo pieces remain separate and arrive in ROOM3.
- shared newspaper combination still works.
- no item is directly fabricated to repair continuity.
- ROOM2 and ROOM3 complete end to end with placeholder UI.

### Manual Unity

- the independent hook is visually identifiable and disappears or receives a localized removal presentation after acquisition;
- phone has no ROOM2 `사용` action, while ROOM1 phone behavior remains unchanged;
- before activation, USB drag visibly exposes and successfully uses the computer target; success removes the USB and target, displays the memo screen, and later computer interaction reopens the memo;
- successful hook drop refreshes the current sink view immediately, disables the old drain target, and enables the photo hotspot;
- inventory names are `사진 1`–`사진 4` and do not expose X/Y/Z/W;
- enlarger click offers `확인한다 / 사용한다 / 취소`;
- `사용한다` opens the empty-floor workspace with Back;
- direct photo drop on the enlarger opens that photo's result image;
- result view still accepts another photo and replaces the Sprite without requiring re-entry;
- wrong items, inactive targets, and empty-space drops preserve state and ownership;
- keypad input can be corrected after errors, `3412` unlocks, and onward eligibility reports missing pieces precisely;
- clue text and target highlighting are legible at the target reference resolution;
- ROOM3 mirror evidence state is captured later during ROOM3 implementation.

## 14. Definition of done for the first pass

The ROOM2/ROOM3 placeholder implementation is done when:

- all confirmed Specs' rule transitions are implemented and tested;
- both rooms can be completed through placeholder UI;
- required items remain distinct and do not duplicate;
- clues can be reinspected;
- ambiguous mechanics remain unimplemented behind explicit integration points;
- ROOM1 behavior remains passing;
- required ROOM2 drag/drop and replaceable hook/enlarger image bindings are complete without pulling final polish, ad, ROOM4, ROOM5, or ending work into scope;
- the user can manually play the complete ROOM2 and ROOM3 flows and report UX/content changes independently of rule correctness.
