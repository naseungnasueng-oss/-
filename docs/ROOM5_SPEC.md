# ROOM5 Gameplay/Product Specification

**Status:** Current user-confirmed interaction contract for the integrated ROOM1–5 playtest. Narrative and artwork acceptance remain separate from implementation and compilation. The latest QA amendment below restores student-ID click as the automatic ending trigger, superseding the earlier explicit-final-confirmation contract.

Implementation facts and remaining limitations: [ROOM5_CURRENT_SNAPSHOT.md](ROOM5_CURRENT_SNAPSHOT.md). Shared inventory, transitions and timer policy: [SHARED_CURRENT_SNAPSHOT.md](SHARED_CURRENT_SNAPSHOT.md).

## Latest QA amendment — direct pickup, reading and ending

Understood as: match ROOM3-style pickup-and-enlarge for normal collectibles, require opening the locked cabinet before desk movement, automatically accept the correct lock combination with shared feedback, close cabinets through their doors, replace note/ID detail art, and treat student ID as a noncollectible ending trigger. These rules override conflicting earlier choices/confirmation clauses below.

- Interview record and betrayal note: click once to acquire and show the full inspection image. Only successful ownership opens the preview; capacity failure leaves the object available. No inspect/acquire/close choice panel. Outside the aspect-fitted image closes the preview without activating the scene behind it. Inventory confirmation reopens the same detail; outside dismissal returns to inventory when reading began there.
- `HasOpenedDoyunLocker` gates desk movement and penalties. Correct dial combination unlocks, waits for `InteractionTimingSettings.UnlockDelay` (default 1 second), then opens the cabinet. Closing/reopening later does not revoke earned desk access. The correct desk and once-per-wrong-desk penalty rules remain unchanged.
- Each dial change checks whether the full combination matches; there is no submit button and intermediate combinations remain editable. During success feedback, room interaction is blocked while the shared timer continues.
- Open cabinet door surfaces are close targets, not separate labeled close buttons. Empty Doyun cabinet can close/reopen after note pickup. The numeric inspection overlay still has its exit control for aborting an unsolved lock.
- Student ID click does not acquire anything. It starts the [presentation ending](PRESENTATION_RELEASE.md): stop the shared clock → change the gauge person to police and remain on the current view until the one-shot knocks finish → supplied flashback video → start screen. No extra final-truth confirmation, outside-image dismissal or old text/ID Next pages.
- New originals `ArtSource/5번방_쪽지.png` and `ArtSource/5번방_학생증.png` are copied unchanged to the corresponding `_QA.png` files in `Assets/5번방/누끼/`. Library and builder bind their detail sprites. UI crops transparent canvas margins for legible enlargement; originals and prior assets are preserved.
- Interview record is displayed at width 1000 in a 1000×760 clipped viewport, preserving aspect ratio, with vertical wheel/drag scrolling. Field and inventory readings use the same view and reopen at the top. ROOM5 initializes EventSystem/InputSystemUIInputModule for actual scroll dispatch; merely adding ScrollRect was insufficient. Outside-viewport press dismisses without scene clickthrough. Note and student-ID display are not scrollable.
- Separate guidance, hover and result-message bars are hidden. Artwork clues, operation controls and ending text remain. Exception: successful first acquisition of the betrayal note shows both complete sentences at the bottom of the screen: “민준의 책상을 움직이시오.” and “다른 사람 책상을 움직이면 시간이 1분 차감됩니다.” The display lasts 15 realtime seconds and stays above the note inspection, with a taller text area, reduced padding and font best-fit to prevent the penalty sentence being truncated. Ordinary messages must not cancel it; the shared timer continues. Unlocking/opening/reopening a cabinet, failed/duplicate pickup and save restoration do not trigger or restart it. Expiration/ending can dismiss it.
- Smoke expectations cover desk prerequisites, pickup previews, note inventory reading, scroll geometry/input infrastructure and direct student-ID ending. Runtime/editor source compilation passed; the user accepted the local scroll correction. Updated automated Play Mode and complete browser playthrough remain unexecuted.

## 1. Purpose and continuity

ROOM5 re-presents the five-person festival preparation committee through five desks and five lockers. The intended narrative is that four students coordinated their statements against 김민준; the student ID connects his name to the face seen in ROOM3.

- Enter from ROOM4 through its explicit next-room button.
- Preserve the actual remaining inventory, including ROOM3's student interview notice. Do not seed default items or remove leftovers on entry.
- Continue the same one-hour session clock. Inspection and cabinet interactions do not pause it. The ending starts the terminal presentation and stops the clock.
- ROOM3's **학생 면담 통지서** is distinct from ROOM5's **학생 면담 기록** and 도윤's **구겨진 쪽지**.
- Inspecting/collecting the ROOM5 interview record is optional. Do not add an acquisition prerequisite to the cabinet, desk or ending flow.

## 2. Player flow

```text
ROOM4 → explicit continuation → ROOM5
  ├─ open the blackboard and read its writing
  ├─ inspect the interview record on the lower-center desk; optionally acquire it
  └─ open 도윤's numeric lock → 3142
       → cabinet opens in the existing locker-wall view
       → inspect / acquire / cancel the note
       → compare the note with the desk handwriting
       → move desk 3 (lower-left)
       → 김민준's cabinet is unlocked; all desk movement interactions end
       → open that cabinet in place
       → student ID: inspect / cancel, without acquisition
       → inspect the ID image; close or explicitly select “마지막 진실 확인”
       → ending, exactly once
```

The lower-center desk carrying the record is **desk 4**, not the correct desk 3. A correct answer may be entered without a mandatory record/note inspection sequence; this is not a forced checklist puzzle.

## 3. Blackboard

- Opening reveals the supplied opened-blackboard artwork.
- Once opened, remove its completed interaction hotspot. The writing remains visible; there is no repeated open action.
- Intended narrative text remains `다들 아무 일 없었다는 듯 졸업했다.` / `나만 그날에 멈춰 있다.` Its artwork correspondence still needs content review.

## 4. Interview record

- The document records four students' statements about the fire on `2015-05-20`, connecting it to ROOM3's notice.
- It is a separate paper object on desk 4, with a matching independent click target, over a paper-free desk background.
- Moving a desk must not make an uncollected record unavailable. Its image and hit area follow its desk's displayed pose.
- Click offers **확인 / 획득 / 취소**. Inspection opens the document without acquiring it. Acquisition alone adds it to inventory and removes the field object and target; failed inventory insertion must not remove it.
- Field and inventory inspection use the document-only cutout `Assets/5번방/누끼/5번방_면담종이.png`, not the whole desk-background illustration. The small field paper uses the separate `5번방_책상_면담기록.png` cutout.
- After acquisition, selecting the record in inventory and confirming opens that same full document. Closing returns to inventory; reading neither consumes it nor starts another puzzle.
- Acquisition is optional. The player can read in place and leave it behind.

## 5. Cabinets and note

### In-place cabinet interaction

- The base view remains the wall of five lockers. Opening a cabinet changes that cabinet in place, not the navigation surface or history.
- Both cabinets can remain open together. Leaving and revisiting the wall preserves their states.
- Open doors, content hit areas and explicit close controls are separate interactions. There is no cabinet-interior “back” step.
- Existing open-cabinet artwork may be cropped and layered; no new complete door-art production is required by this contract. Visual alignment is an acceptance condition, not assumed from compilation.
- Only the numeric lock may use a separate inspection overlay. Dismissing it returns to the unchanged wall.

### 도윤's cabinet

- Correct password: **3142**. Incorrect input preserves the locked state and permits retry.
- Password-unlocked and door-open are different facts. Once unlocked, closing must not require solving the lock again.
- Before note acquisition, the open cabinet has a separate close control and a note target.
- The note offers **확인 / 획득 / 취소**; inspection does not acquire. Inspection uses a dismissible overlay, not a navigation-history step.
- Acquisition removes the note and its target. As with ROOM1's completed drawers, the empty cabinet remains open and its door/close interactions retire.

### 김민준's cabinet

- Initially locked. Moving desk 3 unlocks it; clicking it opens it in place.
- Closing and reopening preserves the unlock state.
- The student ID remains physically inside: field inspection does not remove it. Cabinet closing does not erase inspection progress.

## 6. Desk puzzle

- The intended clue is four handwriting styles in the note matching four of the five desks; the absent style identifies 김민준's desk. Legibility and the unique four-of-five correspondence require content QA.
- Current correct desk: **3**, at the lower left. Desk 4 is the lower-center desk with the interview record.
- Before success, a wrong desk deducts **60 seconds once per desk**. Retrying the same already-moved wrong desk does not deduct again.
- Moving desk 3 unlocks 김민준's cabinet and ends the movement puzzle. Disable **all five desk-movement hotspots**, and reject further movement or penalties in the controller.
- The record's independent target remains usable until acquired, including after the desk puzzle is solved.
- Independent persistent rendering of every moved desk is not part of this pass. The current last-moved-background limitation is recorded in the snapshot and must be evaluated during full-run QA.

## 7. Earlier student-ID/ending proposal — superseded

The latest QA amendment and [presentation release](PRESENTATION_RELEASE.md) replace the following earlier confirmation/text-page proposal; it is retained only as narrative history.

- Clicking the ID offers **확인 / 취소**. Neither clicking nor cancelling automatically acquires anything or starts the ending.
- Inspection displays the name/photo document and records that it has been inspected. **No StudentId inventory item is created.**
- The inspection overlay offers **닫기** and the separate **마지막 진실 확인** action. Closing permits later reinspection.
- Only the explicit final action starts the ending, once, after inspection and while time remains. There is no extra puzzle or password.
- Starting the ending closes inventory and stops the shared clock. Before that explicit commitment, the clock continues.
- The third/final page displays **게임 클리어**, not a restart/처음부터 action. Activating it again must retain the completed state without resetting inventory, time or the scene.
- Hide room-view name tags (including `5번 방 · 교실`); the first ending page uses `마지막 진실` rather than `ROOM 5 CLEAR`. Other existing hint sentences remain unchanged.

### Narrative sequence still requiring acceptance

The earlier narrative brief proposes these ordered recollections:

1. ROOM3 mirror face → student-ID photo: `내 이름은 김민준이었다.`
2. Five-person committee photograph: `나는 준비위원회 5명 중 한 명이었다.`
3. Interview record → 도윤 note → handwriting comparison: four students shifted responsibility to 김민준.
4. ROOM4 disciplinary record → court article: discipline, criminal punishment and departure from school. Exact departure wording is unapproved.
5. ROOM4 tracking plan: `복수를 계획했다.`
6. ROOM2 four photos: `모두를 죽였다.`
7. ROOM1 sealed-room opening: survival after a suicide attempt and memory loss; final wording is unapproved.

This is a narrative target, **not a claim that the current three-page ending implements a seven-shot montage**. Final narration, photo/mirror identity match, and how optional or skipped upstream evidence is recalled remain review items. No unimplemented backtracking/evidence gate should be presented as currently working.

## 8. Acceptance checklist

1. **Continuity:** actually play ROOM4→5; inventory identities/sprites and elapsed time survive without seeding or reset.
2. **Completed blackboard:** opens once, writing remains, completed hotspot no longer responds.
3. **Record without acquisition:** inspect, close and revisit without adding inventory ownership.
4. **Record after desk movement:** move desk 4 and another desk; paper and click target remain on the displayed desk. Acquire afterward; both disappear.
5. **Inventory reading:** acquire, select, confirm, read the document-only image, close to inventory and read again; ownership is unchanged.
6. **Lock retry:** wrong code does not open; 3142 unlocks; close/reopen does not ask for the code again.
7. **Note choices:** inspection and cancel leave ownership unchanged; acquisition removes only the note and leaves the cabinet open.
8. **Desk penalty:** each new wrong desk deducts 60 seconds once. Correct desk 3 unlocks the cabinet and removes every movement target, not the document target.
9. **Cabinets in place:** open both, navigate away/back, inspect contents and use explicit close controls where allowed. No interior-view history entries; no crop seams or stale content hotspots.
10. **Student ID:** cancel does nothing; inspection shows the ID without acquisition or automatic ending; closing allows another inspection.
11. **Ending commitment:** only the explicit final action starts it; timer stops and duplicate triggers do not restart it.
12. **Expiration and UI isolation:** expiration prevents progression; overlays and inventory do not click through to doors, desks or the ending. Verify transition clicks do not activate destination hotspots.

Evidence means actual observed state, readable art and inventory/time checks. Direct C# compilation or a success log alone does not clear these scenarios. Updated smoke tests are not counted as executed unless run in Unity.

## 9. Remaining decisions and boundaries

- Full-run visual and interaction QA; simultaneous cabinet crop alignment and document placement.
- Unique handwriting mapping, derivation/order of the cabinet code, exact blackboard/narrative consistency.
- Last-moved-only desk rendering versus independent desk states if the current behavior is unacceptable in play.
- Final ending content and skipped-evidence continuity; save/load and replay policy.
- No additional mandatory record acquisition, new puzzle, broad refactor, or upstream rule change is implied by this specification update.
