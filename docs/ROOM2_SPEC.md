# ROOM2 Gameplay/Product Specification

**Status:** Confirmed ROOM2 gameplay/UX contract. ROOM1→ROOM2→ROOM3 transition and shared-inventory continuity are integrated. Remaining room-local follow-up is limited to broad hotspot/presentation optimization and replacement with the final enlarger asset; timer behavior and save/load persistence remain later shared-integration work.

## Current QA interaction amendment

Understood as: apply ROOM1-style hidden guidance and direct field pickups to ROOM2, and align the left-wall computer-area drawer's open/close targets to the rendered drawer rather than the whole cabinet. This amendment overrides older conflicting interaction descriptions below.

- Hide the separate context/hover hint and transient result-message text. Preserve monitor memo text, photo/mapping clues, keypad controls, Back and completion controls. Shared hotspot emphasis is unchanged.
- Clicking the floor hook, drawer photo fragment, or drained-sink photos acquires directly without inspect/acquire/cancel choices. Controller-owned prerequisite, duplicate and capacity checks remain; the four-photo set still uses its existing acquisition path, marking the field set collected only after all four items are owned. That path is not atomic: a capacity failure can leave some photos already owned, with the field interaction available to retry. Reduced 12-slot full-run capacity needs QA.
- Clicking the enlarger directly opens its base workspace without a use/inspect/cancel modal. **확대한다 remains**: loading a photo does not automatically magnify it.
- Keypad input is constrained to the original red display interior, with a transparent UI background: reference rect `(559.2,113.5,266.8,75.6)` derived from the aspect-fitted 343×534 keypad art. Preserve digit entry/raycasting; do not paint over the display frame.
- Successful door code waits for the shared unlock feedback duration (default 1 second), then requests the success overlay automatically, without a second door click. Both old-photo ownership prerequisites and expiration guards remain. The overlay's explicit ROOM3 button, not keypad release, loads the next scene. Missing pieces still require collecting them and using the unlocked door afterward.
- Drawer opening targets only the closed middle drawer front `(960,715,270,145)`; closing targets the rendered open front `(1095,885,295,65)`, in the room's reference coordinates. Only the target for the current state is enabled. The close target is below the photo pickup area `(1000,700,220,180)` and does not overlap it. The empty drawer remains closable/reopenable without respawning its photo.
- `Room2PlayerNavigationViewTest` expectations now cover direct pickups, hidden guidance and empty-drawer reopen. Actual Play Mode/pointer QA of this revision remains pending. Source changes do not update an existing WebGL package or hosted session.

## 1. Source and context binding

This specification is bound to:

- the confirmed ROOM2 context and requirements on the current integration line;
- the shared inventory and transition behavior in [`Assets/Scripts/InventoryManager.cs`](../Assets/Scripts/InventoryManager.cs), [`Assets/Scripts/Room1GameController.cs`](../Assets/Scripts/Room1GameController.cs), and the ROOM2 modules under [`Assets/Scripts/Room2`](../Assets/Scripts/Room2), plus ROOM1/ROOM2 editor coverage.

The shared inventory establishes ownership, one-time acquisition, fixed-panel presentation, generic item-drag release behavior, and cross-scene item continuity. ROOM2 owns target resolution and puzzle meaning. ROOM1 transitions explicitly into ROOM2, and ROOM2 transitions into ROOM3 only after both old-photo pieces are owned. Timer behavior, save/load persistence, and missing raster art remain separate integration concerns.

Throughout this specification:

- **Confirmed** means required by the supplied ROOM2 intent.
- **Integration assumption** means needed to describe ROOM2 against expected shared behavior but not yet guaranteed by an API.
- **TBD** means a product or integration choice remains unresolved and must not be silently decided during implementation.

## 2. Purpose, success terminal, and non-goals

### Purpose

Define the bounded, player-observable ROOM2 experience in a darkroom: discover and inspect two independent clue chains, deduce the exit code, unlock the exit, and preserve two separate old-photo pieces with story foreshadowing.

### Player-visible success terminal

ROOM2's keypad succeeds when the player enters `3412` and the exit unlocks. A wrong code must not unlock the exit. Entering ROOM3 additionally requires both old-photo pieces; keypad success alone does not bypass this transition gate.

### Explicit non-goals

This specification does not:

- prescribe Unity class layout, scene hierarchy, prefabs, or unconfirmed asset choices;
- redesign ROOM1, its puzzles, or its owned shared systems;
- define new story, identities, relationships, dialogue, or puzzle mechanics beyond the confirmed material;
- make the old-photo story clue a prerequisite for accepting code `3412`; any transition constraint exists only to preserve required cross-room item continuity;
- conflate the four sink photos with the two-piece old photo;
- require a ROOM2-specific timer rule, time limit, penalty, advertisement/rewarded-ad hint, or reset loop;
- choose persistence/serialization, UI, audio, animation, or input implementation where the requirement is silent.

## 3. Preconditions and continuity assumptions

### Confirmed preconditions and continuity invariant

- ROOM1, ROOM2, and ROOM3 form a mechanically connected item-continuity chain. ROOM4 has narrative continuity, but no ROOM3-to-ROOM4 carried-item precondition is currently confirmed.
- ROOM2 is a darkroom whose required prior-room inputs are:
  - the ROOM1 USB;
  - the ROOM1 left/first old-photo piece.
- ROOM1 completion requires the unlocked exit, USB and left old-photo piece before the explicit `2번 방으로` transition becomes available. Phone acquisition is optional; newspaper is field-read-only. See [ROOM1 current spec](ROOM1_CURRENT_SNAPSHOT.md).
- ROOM1 stops only its own timer, closes the inventory window, preserves inventory ownership, and loads ROOM2.
- ROOM2 permits keypad unlock independently, but entry into ROOM3 is blocked until both the carried left piece and ROOM2's right piece are owned. Missing items are never fabricated or directly granted.

### Integration assumptions and TBDs

- **Confirmed inventory continuity:** acquired carried items remain distinct and available across the mechanically connected rooms through the shared persistent inventory instance.
- **Confirmed inventory continuity:** ROOM2 preserves carried ROOM1 inventory, including required USB and left old-photo piece and the phone only if acquired. The phone exposes no `사용` action in ROOM2. Newspaper ownership is not a normal ROOM1 carryover requirement; standalone bootstrap fixtures may still seed legacy items.
- **Confirmed interaction:** target-required ROOM2 items use drag/drop from the fixed compact inventory. Selected-item-then-click is not a second normal player path.
- **Confirmed consumption and retention:** successful computer use removes the USB from inventory after recording memo access. The hook and all four sink photos remain owned after successful use. Room progress is recorded separately from inventory ownership.
- **Confirmed timer boundary:** ROOM1 stops only its own timer before transition. ROOM2 does not automatically reuse or create that timer; no ROOM2-specific timer behavior is confirmed.
- **Confirmed scene handoff:** inventory ownership survives ROOM1→ROOM2 and ROOM2→ROOM3 scene loads through the shared persistent inventory instance.
- **TBD:** save/load serialization and persistence lifetime beyond the active play session.

## 4. Observable room flow

The following dependencies are required, but independent branches need not be forced into a single order:

1. **Computer clue branch:** use the carried USB on the computer, then read the computer memo. Once revealed, the memo remains re-openable.
2. **Sink-photo branch:** acquire the independently presented old metal hook from the floor; drag it onto the sink drain target; immediately replace the drain interaction with the drained-sink/photo interaction; acquire inventory items named `사진 1`, `사진 2`, `사진 3`, and `사진 4`; load each photo into the enlarger by direct drop or workspace drop; click `확대한다` to produce and reinspect each magnified mapping result.
3. **Deduction and keypad:** combine the memo's death order with the four photo mappings to derive `3412`; enter it at the exit keypad to unlock it.
4. **Separate story branch:** open a ROOM2 drawer, acquire and reinspect the blurry right old-photo piece, and preserve both separate old-photo pieces for placement and completion in ROOM3. ROOM2 may present the confirmed foreshadowing monologue.
5. **Onward continuity:** ROOM3 transition is dispatched only when both the ROOM1 left piece and ROOM2 right piece are owned.

The computer and sink-photo branches may be explored in either order. The story branch may be explored before, during, or after the code-clue branches and is not a prerequisite for the keypad to accept `3412`. The keypad may be inspected or attempted before all clues are found; only the correct code unlocks it. Unlocking does not bypass the separate two-piece ROOM3 transition gate.

## 5. Object and item catalog

| Object/item | Origin/status | Acquisition or use | Required persistence / inspection behavior | Consumption status |
|---|---|---|---|---|
| Phone | Optionally carried from ROOM1 | No standalone use in ROOM2 | If owned, remains owned, but its ROOM1-only `사용` action is hidden in ROOM2 | Retained if acquired |
| USB | Carried from ROOM1 | Dragged onto the computer closeup's right-hand rectangular I/O panel before activation | Switches the closeup to the USB-inserted image and renders the memo inside the monitor; after success the USB target is inactive, while repeat computer interaction keeps the memo accessible | Consumed after successful use |
| Newspaper | Carried from ROOM1 | No new ROOM2 puzzle role | Remains owned without creating a ROOM2 action | Retained |
| Old-photo left piece | Carried from ROOM1 | Preserved as a separate carried item for ROOM3 | Must remain available through ROOM2 and its transition to ROOM3 | Retained; ROOM3 owns later placement/completion behavior |
| Computer memo | Revealed ROOM2 clue; not necessarily an inventory item | Available after USB use | Must be re-openable and show the exact four statements | Retained as accessible clue |
| Old metal hook | Acquired from an independent darkroom-floor object using its supplied cutout as room and inventory art | Dragged anywhere onto the rectangular sink basin | Cannot duplicate; successful use refreshes the same sink view immediately and disables the completed drain target | Retained after use |
| Sink drain plug | Room object | Hook removes/pulls it; hand interaction cannot | Removed/drained result remains progressed; after success, only the newly available photo interaction remains active | Not an inventory requirement |
| Four sink photos | Acquired from sink after draining and presented as `사진 1`–`사진 4`; all four inventory cards use the same supplied drain-photo image | Each is dragged onto the enlarger object or its workspace floor, then explicitly enlarged with `확대한다` | Internal identities remain distinct for deterministic results, but inventory art need not distinguish them. Only explicit enlargement reveals `사진 1→Z=3`, `사진 2→W=4`, `사진 3→X=1`, `사진 4→Y=2` | Retained after use |
| Enlarger | Room object and reusable work surface | Click opens `확인한다 / 사용한다 / 취소`; direct photo drop loads that photo into the workspace without magnifying it | `사용한다` opens a base floor image with Back and an active drop area; each valid photo drop selects that photo's loaded image and enables `확대한다`; from a magnified result, first Back restores that photo's loaded image and second Back returns to the enlarger closeup | Not applicable |
| Old-photo right piece | Acquired from a ROOM2 drawer; blurry | Preserved as a separate carried item for ROOM3 | Must not duplicate on repeat drawer interaction; remains reinspectable in its blurry state | Retained; ROOM3 owns later placement/completion behavior |
| Exit keypad/door | Room object | Accepts code entry | Wrong codes remain locked; `3412` unlocks; ROOM3 transition additionally requires both old-photo pieces; progress does not revert on repeat interaction | Not applicable |

**Separation rule:** the four sink photos are the enlarger/code clue set. The left and right old-photo pieces belong to one separate story-photo continuity. No item, mapping, or progress state may treat these sets as interchangeable.

## 6. Puzzle contracts

### 6.1 Computer and USB

**Confirmed contract**

- Without an available USB, the computer must not reveal the memo as though the USB had been used.
- Dragging the carried USB onto the rectangular I/O panel on the computer body's right front makes the memo available. The valid target covers the panel rather than a single small port and is visibly emphasized while the USB is being dragged.
- A draggable USB released without an active target does not count as use.
- Successful use removes the USB from inventory, switches the closeup from the unplugged image to the supplied USB-inserted image, renders the clue text inside the blue monitor area, and immediately disables the completed USB drop target.
- Once available, the USB-inserted image and monitor text persist when the computer is revisited; no second USB is required.
- The memo must display this exact wording, numbering, and punctuation; visual typography and line wrapping may vary:

  ① X가 죽었을 때 Y와 W 중 정확히 한 명만 살아 있었다.  
  ② X와 Z는 연달아 죽지 않았다.  
  ③ Y와 Z는 연달아 죽지 않았다.  
  ④ W가 죽었을 때 X와 Y는 모두 살아 있었다.

The ROOM2 inventory card for the carried phone does not expose its ROOM1-only `사용` action. This room-context presentation rule must not remove the phone or regress its ROOM1 behavior.

### 6.2 Hook, sink, and drain

**Confirmed contract**

- The old metal hook is collectible from an independent room-object image/layer rather than being treated as background state.
- The sink initially contains water and has a drain plug.
- Trying to remove the plug by hand does not remove it or drain the sink.
- The active hook-drop target follows the inner rectangular sink-basin boundary rather than only the small drain plug or the oversized outer counter rim. Dragging the hook anywhere onto that basin pulls the plug and drains the water; the hook remains owned.
- The same sink view refreshes immediately after success. The completed drain target becomes inactive, and the same basin region becomes the four-photo acquisition hotspot without requiring a Back/re-entry round trip.
- The four sink photos become acquirable only after draining.
- Once drained, repeat sink interaction does not restore the water, plug, or a duplicate photo set.

### 6.3 Enlarger and four sink photos

**Confirmed contract**

- The enlarger operates on the four sink-photo contents, not on either old-photo piece.
- Inventory retains four internal contents named `사진 1`, `사진 2`, `사진 3`, and `사진 4`, but all four cards use the same supplied drain-photo inspection image. X/Y/Z/W are hidden reveal results, not item names or distinct inventory art.
- Clicking the enlarger directly enters its base workspace: the empty floor, active photo-drop area, and Back. No inspect/use/cancel choice is shown.
- A carried sink photo may also be dragged directly onto the enlarger object, opening the same workspace with that photo loaded but not magnified.
- In the workspace, dragging a photo onto the floor replaces the current presentation with that photo's non-magnified loaded state. It must not reveal X/Y/Z/W, display `X=1`-style text, call the Rules-owned photo inspection/reveal operation, or mark the photo revealed.
- A clear `확대한다` button is visible and active only when a valid photo is loaded and not yet magnified. Clicking it is the only path that calls the Rules-owned photo inspection/reveal operation.
- After `확대한다`, the workspace switches to the magnified presentation/result and only then displays the mapping. Result state does not disable the workspace drop area. Another photo can be dropped immediately, replacing the result with that new photo's loaded state and requiring `확대한다` again.
- Back is state-sensitive inside the workspace. From a magnified result, the first Back stays in the workspace and returns the same photo to its loaded non-magnified image with `확대한다` available again. A second Back from that loaded state returns to the enlarger closeup and closes the workspace. Back from the empty base also returns directly to the enlarger closeup. Re-entering or re-dropping an already revealed photo follows the same loaded-then-magnified sequence and reproduces the deterministic result.
- The four magnified result contracts follow the delivered source-folder order: `사진 1→Z=3`, `사진 2→W=4`, `사진 3→X=1`, and `사진 4→Y=2`. The photo numbers are inventory handles only; the magnified marks and the USB memo establish the keypad deduction.
- All four photos remain owned after use.
- An unrelated item or inactive-area drop does not fabricate a loaded state, magnified result, or mapping; it does not consume an item or mark a photo as processed.

### 6.4 Deduction contract and verification

Use the natural interpretation specified by the source intent:

- “when A died, B was alive” means B dies later than A;
- “exactly one alive” refers to that time;
- “died consecutively” means adjacent positions in the death order.

Under that interpretation, the memo has a unique order:

1. Statement ④ gives `W < X` and `W < Y` (where `<` means “dies before”).
2. At X's death, W is therefore already dead. For exactly one of Y and W to be alive in statement ①, Y must still be alive, so `X < Y`.
3. Thus `W < X < Y`. Insert Z into the remaining position. Placing Z between any members or after Y makes Z adjacent to X or Y, violating statement ② or ③. Only placing Z before W satisfies both non-adjacency statements.
4. The unique death order is therefore `Z → W → X → Y`.

Applying the photo mappings yields `Z,W,X,Y → 3,4,1,2`, so the final keypad code is **`3412`**.

This deduction is player reasoning; the specification does not require an additional in-game ordering UI, automatic solver, or explicit clue-completion gate.

### 6.5 Keypad and exit

**Confirmed contract**

- Entering exactly `3412` unlocks the exit door.
- Any other submitted code does not unlock it.
- A wrong attempt does not erase acquired items, revealed clues, drained-sink progress, or story progress.
- When `3412` is accepted, the keypad remains visible and non-adjustable with `잠금이 풀렸다` feedback for one second, then automatically returns to the door closeup and clears that transient message.
- Once unlocked, the exit remains unlocked. Until dedicated unlocked-door art exists, a code-rendered unlocked indication is sufficient. ROOM3 transition remains blocked until both old-photo pieces are owned, with precise feedback for the missing left or right piece.
- No requirement mandates that the system verify the player inspected every clue before accepting `3412`; keypad acceptance and onward-transition eligibility are distinct outcomes.

### 6.6 Separate old-photo story clue

**Confirmed contract**

- The computer-area drawer contains the blurry right/second old-photo piece, which depicts or indicates one person. The stable closed background remains registered while the delivered open-drawer region is composited locally.
- Opening the drawer permits one-time acquisition of that piece, and the acquired piece remains reinspectable in ROOM2.
- The ROOM1 left piece depicts or indicates four people. The left and right pieces remain separate carried items throughout ROOM2 and are handed forward separately to ROOM3.
- ROOM2 does not combine, clarify, or complete the two old-photo pieces. Actual placement and completion occur in ROOM3 inside the activity album.
- ROOM2 may communicate the suspected connection with this exact monologue:

  > “첫 번째 사진엔 네 명, 이번 사진엔 한 명… 이 다섯 사람이 서로 관련된 건가?”

- This is story foreshadowing only. It must not contribute a digit, reorder the memo, stand in for a sink photo, or gate acceptance of code `3412`. The right piece gates only the subsequent ROOM3 transition because ROOM3 requires it.

## 7. Required persistent progress facts

The ROOM2 experience must be able to preserve these facts for as long as the relevant play/session continuity requires. These are domain facts, not a prescribed class, file, or serialization model:

- whether the ROOM1 USB is still available or has been consumed by successful use, and whether it has enabled the ROOM2 computer memo;
- whether the memo is available for reopening;
- whether the floor hook has been acquired;
- whether the sink plug has been pulled and the sink has drained;
- whether the four sink photos have become available and which have been acquired;
- for each numbered sink photo, whether its image-based enlarger result is available and remains re-inspectable;
- whether the enlarger workspace is closed, showing its base floor, holding a particular loaded photo, or showing that loaded photo magnified; loaded/magnified presentation must remain explicit Controller state rather than inferred from the active Sprite or UI text;
- whether the ROOM2 right old-photo piece has been acquired and remains available for reinspection;
- whether the exact optional foreshadowing monologue has been presented;
- the separate availability of both old-photo pieces for the ROOM2-to-ROOM3 handoff;
- whether the exit door has been unlocked;
- whether the player has left/completed ROOM2;
- retained ownership of the ROOM1 left old-photo piece during ROOM2;
- ownership of the ROOM2 right old-photo piece before ROOM3 transition, without fabrication or direct grant.

Repeat interactions must not roll these facts backward or award duplicate one-time acquisitions.

## 8. Failure and feedback contracts

Feedback wording and presentation are Design concerns unless exact text is specified above, but the player must receive a perceptible response rather than a silent false success.

| Situation | Required observable outcome |
|---|---|
| Computer use attempted without USB | Memo is not newly revealed; interaction indicates that the required means/item is unavailable or missing without inventing a substitute solution |
| Sink basin clicked by hand | Plug remains in place, water does not drain, and feedback communicates that hand removal does not work; the same full-basin region accepts a valid hook drop |
| Hook use attempted while hook is unavailable | Sink remains unchanged; no success state or photos are awarded |
| Photos sought before sink drains | Four-photo set is not acquirable through the water/plug state |
| Enlarger inspected without choosing use | Only explanatory prose appears; no workspace/result or progress is fabricated |
| Unrelated item dropped on the enlarger or workspace floor | No loaded state, magnified result, or mapping is fabricated, no unrelated item is consumed, and no photo reveal is falsely recorded |
| Revealed-result workspace receives another numbered photo | The current result is immediately replaced by the new photo's non-magnified loaded state; mapping hides again, `확대한다` becomes active, and the drop area and Back remain usable |
| Back pressed on a magnified photo result | The same photo returns to its loaded `확대전` image without leaving the workspace; the next Back returns to the enlarger closeup |
| Wrong keypad code | Door remains locked; existing progress remains intact; another attempt remains possible |
| Previously collected one-time item is targeted again | No duplicate inventory content is granted |
| Computer revisited after USB success | Memo reopens without a USB target or another USB requirement |
| Processed/revealed sink photo revisited by drop/reinsertion | That photo first appears loaded and non-magnified again; clicking `확대한다` deterministically restores the same mapping |
| Sink drains successfully | The same view immediately disables the drain target and enables the photo interaction without requiring re-entry |
| Drained sink revisited | Sink remains drained; the old drain target stays inactive and no duplicate photos are granted |
| Opened drawer revisited | Drawer remains open/available as appropriate and does not grant another right piece |
| Acquired right old-photo piece revisited | The same blurry piece remains inspectable; no duplicate piece or clear/completed photo is produced |
| ROOM1 exit attempted without USB or left old-photo piece | ROOM1 completion/transition is refused until the actual missing item is acquired; no substitute is fabricated or directly granted |
| `3412` accepted while the right old-photo piece is missing | Keypad unlock remains valid, but ROOM3 transition is refused with precise missing-right-piece feedback until the actual piece is acquired |
| Advertisement/rewarded-ad hint used, if such assistance exists | It provides location-only guidance, such as `ROOM 1의 서랍을 다시 확인해보세요.`; it does not grant, spawn into inventory, fabricate, or auto-complete a USB or photo piece, and viewing it is never required for solving or recovery |
| `3412` accepted | Keypad input retires, `잠금이 풀렸다` remains visible for one second, then the view returns to the door closeup and clears the message |
| Unlocked exit/keypad revisited | Exit does not relock or reject the already-achieved keypad unlock state; onward transition still preserves the continuity invariant |

## 9. Acceptance scenarios and evidence/oracles

The oracle must be the observed room state, visible content, inventory/state inspection, and transition outcome—not an implementation's self-reported “success” log alone.

### A. Happy path to ROOM2 exit

**Given** ROOM2 starts with the USB and left old-photo piece carried from ROOM1  
**When** the player drags the USB onto the computer, acquires the independent hook, drains the sink with it, acquires `사진 1`–`사진 4`, loads each photo through direct enlarger drop and/or the continuous workspace, clicks `확대한다` for each magnified reveal, acquires the right old-photo piece, deduces the code, and submits `3412`
**Then** the memo shows all four exact statements, the photos reveal exactly `Z=3`, `W=4`, `X=1`, `Y=2` in photo 1–4 order, the keypad shows its one-second unlock feedback before returning to the door closeup, the exit remains unlocked, and the player can proceed to ROOM3 with both old-photo pieces still distinct.

**Evidence/oracle:** direct capture/assertion of the exact memo and mappings; inventory/progress evidence for acquisitions; door collision/navigation or room-transition evidence showing the player can leave after `3412`.

### B. Logical clue oracle

**Given** all 24 permutations of X, Y, Z, W and the stated natural-language interpretation  
**When** the four memo constraints are evaluated  
**Then** exactly one permutation satisfies them: `Z,W,X,Y`; applying the four mappings produces `3412`.

**Evidence/oracle:** an independent truth-table/permutation check or equivalent human-reviewed derivation, not the keypad's configured answer by itself.

### C. Memo and photo reinspection

**Given** the USB has revealed the memo and one or more numbered sink photos have produced enlarger result images
**When** the player closes and reopens the memo, re-drops a revealed photo directly on the enlarger, or uses it again in the workspace after other interactions
**Then** the memo remains inspectable, the photo first returns to its loaded non-magnified state with no mapping visible, clicking `확대한다` restores the same unchanged result/mapping, and the workspace still accepts the next photo without requiring re-entry. Back from that result first restores the same photo's `확대전` state; a second Back exits to the enlarger closeup.

**Evidence/oracle:** repeated UI/content observations matched to the specified text/mapping; retained progress observed after intervening interactions.

### D. Wrong-code refusal

**Given** the exit is locked  
**When** the player submits any code other than `3412`, including after collecting some or all clues  
**Then** the exit remains locked, the player cannot leave through it, progress is not erased, and another attempt is possible.  
**When** `3412` is subsequently submitted  
**Then** the exit unlocks.

**Evidence/oracle:** actual locked/unlocked door behavior or room-transition boundary, plus unchanged clue/item progress before and after the wrong attempt.

### E. Unavailable-item interactions

**Given** the relevant item or prerequisite is unavailable  
**When** the player tries the computer without USB, the sink by hand or without the hook, or the enlarger without an applicable sink photo  
**Then** none of those interactions advances its success fact, consumes an unrelated item, or fabricates its output; perceptible refusal feedback is provided. The USB is consumed only by successful use on the active computer target.

**Evidence/oracle:** before/after comparison of inventory and the required progress facts, plus direct observation that the memo, drained sink/photos, or mapping did not appear.

### F. Story clue independence and item-set separation

**Given** the ROOM1 left old-photo piece is carried and the ROOM2 drawer is opened  
**When** the player acquires and reinspects the blurry right piece  
**Then** both pieces remain separate, no clear/completed photo is produced in ROOM2, and ROOM2 may present the exact confirmed monologue without awarding a code digit or sink-photo mapping.  
**And given** the right piece has not been acquired or the optional monologue has not been presented  
**When** the player otherwise submits `3412`  
**Then** the keypad can still unlock, but ROOM3 transition waits for acquisition of the actual right piece.
**When** the player proceeds into ROOM3's mechanically connected album progression
**Then** the left and right pieces are available as two separate items; neither has been fabricated or directly granted.

**Evidence/oracle:** inspection capture of the blurry right piece and, when presented, the exact monologue; inventory/progress evidence that both old-photo pieces remain separate from each other and from the four sink photos; keypad unlock without completing the story branch; precise transition refusal while the right piece is missing; ROOM3 album-progression evidence of two distinct actual pieces.

### G. Repeat-interaction stability

**Given** one-time acquisitions and progress actions have completed  
**When** the player repeats floor, sink, drawer, computer, enlarger/photo loading and enlargement, old-photo, keypad, and exit interactions
**Then** no duplicate hook, photo set, or right photo piece appears; completed progress does not revert; reinserted sink photos require `확대한다` again before mappings show; required clues, including the blurry right old-photo piece, remain inspectable.

**Evidence/oracle:** item counts/identities and progress facts before and after repetition, plus repeated visible clue inspection.

### H. ROOM1/shared-system non-regression

**Given** ROOM2 is integrated with the shared inventory, timer, item acquisition/use, and room transition behavior  
**When** existing ROOM1 acceptance/smoke coverage and a ROOM1→ROOM2→ROOM3 continuity check run, including the phone-gallery exit path and attempts with each required item missing  
**Then** ROOM1's existing puzzle flow still passes; its item selection/use behavior is not broken; ROOM1 stops only its own timer; inventory ownership survives both scene transitions; and the fixed transition gates prevent missing required old-photo pieces without duplication, direct grant, or fabrication.

**Evidence/oracle:** the existing ROOM1 smoke test remains passing, supplemented by shared-system tests and an end-to-end transition observation. ROOM2-only success logs are insufficient evidence of no regression.

## 10. Open integration questions

Only the following unresolved choices remain necessary for later integration:

1. What save/load serialization and persistence lifetime should preserve room progress beyond the active play session?
2. What timer behavior should ROOM2 use? ROOM1 stops only its own timer during the current transition, and no ROOM2 timer contract is approved.
3. Replace the current enlarger presentation when its final asset is delivered, then perform broad hotspot and image-alignment optimization, including the computer closeup, as a later polish pass. Existing localized memo and unlocked-door fallbacks are accepted for the current ROOM2 baseline.

The ROOM1→ROOM2 handoff, ROOM2→ROOM3 item gate, USB consumption, hook/photo retention, shared sink-photo inventory art, and loaded/magnified Back sequence are confirmed and must not be reopened as implementation guesses.

## 11. Design fence

Later Design owns:

- class/module layout and naming;
- Unity scene hierarchy, GameObjects, components, prefabs, and asset selection;
- UI construction and presentation beyond the explicitly confirmed image states, hotspot regions, clue text, and monologue content;
- future changes to the concrete shared inventory and timer APIs;
- serialization, save/load, scene persistence, and data ownership strategy;
- concrete component layout for the confirmed fixed-inventory drag/drop, target highlighting, and pointer handling;
- animations, audio, camera behavior, collision details, and feedback wording not fixed here;
- test harness structure and automation details.

Design and implementation must preserve the observable contracts, dependency boundaries, item-set separation, and explicitly open questions above.
