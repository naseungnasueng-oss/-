# ROOM5 Current Behavior and Structure Snapshot

**Branch:** `integration/room1-5-shared`

**Snapshot point:** Latest direct-interaction QA pass. The [latest ROOM5 amendment](ROOM5_SPEC.md#latest-qa-amendment--direct-pickup-reading-and-ending) overrides older flow details below: normal pickups acquire-and-enlarge, opened Doyun cabinet gates desks, correct dials auto-unlock with feedback, open doors close on click, and student ID directly starts the ending.

**Purpose:** preserve current behavior before playtest-driven fixes and later refactoring. This is a snapshot of the implementation as-is, not a target design.

**Verification:** Current runtime/editor source compilation passed. The user accepted the local interview-record scroll correction after EventSystem/UI input dispatch was added. Revised smoke expectations are authored, not executed. The earlier terminal-clear/title-tag Play Mode pass does not validate this revision; full-run/browser QA remains pending.

**Latest presentation:** Separate hints/hover/result bars are hidden. Interview record uses a 1000×760 viewport with enlarged, vertically scrollable artwork; field and inventory readings reopen at the top and close on outside-viewport press. Note inspection remains a non-scrollable enlargement, and student ID remains a terminal ending trigger. Detail asset bindings are explicitly included in this checkpoint rather than relying on editor-generated rebinding.

Confirmed contract: [ROOM5 spec](ROOM5_SPEC.md). Shared behavior and refactor boundary: [Shared snapshot](SHARED_CURRENT_SNAPSHOT.md).

Primary files:

- `Assets/Scripts/Room5GameController.cs`
- `Assets/Scripts/Room5ImageNavigationUI.cs`
- `Assets/Scripts/Room5ImageLibrary.cs`
- `Assets/Resources/Room5ImageLibrary.asset`
- Scene: `Assets/Scenes/Room5.unity`
- Art: `Assets/5번방/`

### Runtime structure

- `Room5GameController` owns state and rules:
  - `BlackboardOpened`
  - `InterviewRecordCollected`
  - `DoyunLockerUnlocked` / `DoyunLockerOpened`
  - `BetrayalNoteCollected`
  - `MinjunLockerUnlocked` / `MinjunLockerOpened`
  - `StudentIdInspected` (field-only; not an inventory item)
  - `EndingStarted`
  - `TimeExpired`
  - `LastMovedDesk`
- `Room5ImageNavigationUI` owns image views, hotspots, Doyun lock overlay, message UI, ending overlay, and ending page progression.

### Current player flow

1. ROOM5 auto-creates `Room5GameController` when active scene name is `Room5`.
2. Controller ensures a camera exists, gets/creates shared inventory, loads `Room5ImageLibrary`, creates UI, and starts/continues the one-hour shared timer.
3. Player can inspect:
   - overview classroom
   - right wall / blackboard
   - lockers
   - Doyun lock
   - Doyun locker
   - betrayal note
   - desks
   - interview record
   - Minjun locker
   - student ID
4. Blackboard opens once and then displays the opened blackboard image; its completed hotspot is disabled.
   - Note and record click directly acquire and enlarge. Outside-image click dismisses without consuming the owned item. No field choice panel is shown.
   - Once desk 3 unlocks Minjun's cabinet, all desk-movement hotspots are disabled and the controller refuses further desk changes/penalties. The independent document target remains available until acquisition.
5. Interview record is a separate object using the supplied `5번방_책상_면담기록` cutout, placed on the lower-center (fourth) desk. It remains inspectable/acquirable after desk movement. Its hit rect follows its image; both disappear only on successful acquisition. Acquisition is optional, not an ending prerequisite. Field inspection and the acquired inventory card's confirmation both display `interviewRecordDetail`. Inventory reading is non-consuming; closing that overlay reopens inventory for repeat reading.
6. Doyun locker password is `3142`. UI success waits for the shared unlock-feedback duration before closing the numeric-lock overlay; room input and manual close are blocked during that wait. This timing addition is source-only relative to the current web preview and has wiring-test coverage, not yet Play Mode input/timing verification.
7. After Doyun locker opens, betrayal note can be collected and viewed.
8. Desk puzzle:
   - valid desk numbers are 1–5;
   - correct Minjun desk is desk `3`;
   - moving desk 3 unlocks Minjun locker;
   - moving any other untried desk deducts 60 seconds from the shared timer;
   - repeated movement of an already moved wrong desk does not deduct again.
9. Minjun locker can open only after desk 3 unlocks it.
10. Student ID remains noncollectible. Clicking it sets `StudentIdInspected` and immediately starts the ending, displaying the new ID detail on its first page.
11. There is no extra final-truth confirmation or outside-image dismissal for the ending. Subsequent pages and terminal game-clear state remain.
12. Ending:
    - stops timer
    - closes inventory
    - shows three-page ending overlay
    - final page displays `게임 클리어` instead of `처음부터`; further activation retains the final page without restarting or resetting inventory/time.
    - the opening ending heading is `마지막 진실`, not `ROOM 5 CLEAR`; room-view title tags are hidden.

### Current image/state behavior

- Overview uses `overviewWithoutPaper` after interview record collection.
- Right wall uses opened/closed blackboard sprites depending on `BlackboardOpened`.
- Doyun lock can show wrong-lock sprite briefly after failed submission.
- Doyun locker uses note-present vs empty sprite depending on `BetrayalNoteCollected`.
- Desks view still uses the last moved desk sprite after any desk move; otherwise it uses the paper-free background. The document is a separate layer, repositioned with the fourth desk when that moved pose is displayed. This does not yet replace the existing last-moved-only desk background system with independently persistent desk layers.
- Cabinet doors now open directly in the shared lockers view, matching ROOM1 drawer interaction. Cropped regions from supplied open-cabinet sprites overlay the unchanged wall background, allowing both cabinets to remain open. Item hotspots are separate from door hotspots; each open cabinet has its own close control. No cabinet-interior overlay or history entry is opened; only the numeric lock retains an inspection overlay. Doyun unlock and door-open state are separate. After note acquisition, that cabinet remains open with door/item/close interactions retired; Minjun's field-only student ID stays in place and its cabinet can close/reopen. Navigation preserves both door states. Crop seams and simultaneous-open visual alignment await QA.
- Minjun locker keeps its student-ID-present sprite: field inspection does not remove the ID.

### Regression points to verify before refactor

- Verify document inspection/acquisition before and after moving its desk and other desks, plus correct paper/hit-area disappearance on acquisition. Automated smoke coverage was updated but not executed.
- Doyun lock digit cycling and reset/submit operate correctly.
- Wrong Doyun password shows error feedback without opening locker.
- Wrong desk moves deduct exactly one minute once per desk.
- Correct desk 3 unlocks Minjun locker.
- Student ID inspection alone does not start ending; its explicit final-truth action does.
- Ending pages progress, final clear display does not restart/reset, and room-name tags remain hidden. Updated ROOM5 Unity Play Mode smoke passed, including these assertions; final browser visual QA remains pending.
