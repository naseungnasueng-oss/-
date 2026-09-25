# Shared Systems Current Snapshot — ROOM1 / ROOM4 / ROOM5

**Branch:** `integration/room1-5-shared`

### ROOM1–2 QA checkpoint

ROOM1–2 and shared inventory/timer/modal/exit UI use the bundled [font](FONTS.md). `InteractionTimingSettings` supplies a 1-second unlock wait and 2-second message duration; ROOM1/2 guide text is hidden. ROOM1 hotspot emphasis is suppressed. Later-room timing/presentation and WebGL build changes are outside this commit. Runtime/editor source compilation passed; revised Play Mode tests have not been executed.

### Shared hotspot appearance

- Edit `Assets/Resources/HotspotVisualSettings.asset` in the Unity Inspector. `hoverSkin` selects a GUISkin's box style; leave it empty to use the runtime default. `hoverColor` tints that skin (including alpha). `dragTargetColor` controls compatible drag-target emphasis; `buttonHoverBlend` controls tint blending for already-visible buttons without changing their base opacity.
- The confirmed reference is **`C:/Users/Minju/testproject/Assets/Scripts/Room1Prototype.cs`, `DrawHotspot()`**, not the temporary branch copy. It draws nothing normally, and tints `GUI.Box` white with alpha 0.10 on hover. ROOM1–5 now use (1, 1, 1, 0.25) with the box skin rather than a flat color rectangle. The user found 0.20 perceptible but still too faint and requested 0.25; visual acceptance of 0.25 remains pending. `HotspotBoxSkin` captures the runtime box texture and border in `OnGUI`; a sliced uGUI Image draws them so inventory/modal ordering, clipping and input remain with the existing Canvas. No IMGUI controls or overlay boxes are added. Before the skin is ready, the highlight stays transparent instead of flashing a solid fill.
- Hover alpha multiplies the skin's own texture alpha; alpha 1 does not remove texture transparency. Normal field regions clear on exit. Already-visible navigation/control buttons retain their existing backgrounds. ROOM2/3 drag emphasis remains a filled region with alpha 0.58, explicitly resetting the hover sprite before drawing. The shared sprite is reused across rooms and released with its runtime owner. Visual equivalence with the reference is still pending Play Mode QA.
- Position, size, room-specific availability and click actions remain in existing room code. This pass changes appearance configuration only, not hit testing, inventory dismissal or navigation behavior.
- Verify custom colors/alpha in actual play across all rooms. The user observed the box cue at alpha 0.20 and requested 0.25; final all-room contrast acceptance remains pending. Direct compilation does not establish visual acceptance.
- Confirmed scope and closeout plan: [Shared integration spec — current checkpoint](SHARED_SYSTEM_INTEGRATION_SPEC.md#current-integration-checkpoint-interaction-qa-and-test-distribution). No per-room contrast tuning or broad refactor now; pending ROOM4 wall art precedes internal build demonstration and later test distribution.

**Snapshot point:** Shared Back-navigation and box-skin hover pass following ROOM5 checkpoint `d61c372`.

**Purpose:** preserve current behavior before playtest-driven fixes and later refactoring. This is a snapshot of the implementation as-is, not a target design.

**Verification:** Runtime/editor C# direct compilation and diff whitespace checks passed. Updated Play Mode smoke tests have not been run. Cross-room layout, capacity, drag/drop and input regression QA remain pending.

## Room snapshots

- [ROOM1 snapshot](ROOM1_CURRENT_SNAPSHOT.md)
- [ROOM4 snapshot](ROOM4_CURRENT_SNAPSHOT.md)
- [ROOM5 snapshot](ROOM5_CURRENT_SNAPSHOT.md)

## Shared systems used by these rooms

### Inventory

Primary files:

- `Assets/Scripts/InventoryManager.cs`
- `Assets/Scripts/InventoryItemPresentation.cs`
- `Assets/Scripts/IInventoryAccess.cs`
- `Assets/Scripts/Shared/InventoryCombinationPolicy.cs`
- `Assets/Scripts/Shared/InventoryDragTargetResolver.cs`

Current shape:

- Inventory now has **2 columns × 6 rows (12 slots)** in a 250 × 700 reference-unit movable panel. First opening is lower-right with a margin; later openings retain the moved position (clamped if outside the current screen), including room transitions within the session. No automatic deletion of existing items is introduced.
- Ordinary outside pointer-down closes inventory and consumes the input. Exception: each ROOM1–5 view registers its currently available back control. Clicking that control outside inventory passes the same pointer-down to room navigation without closing inventory; the room changes on the first click and inventory remains open. Inventory-covered controls, previews, drags, inactive back controls, room inspection modals and expired/transition-blocked sessions do not qualify. Owner registrations are removed on view destruction. Inventory retains its earlier execution order so it decides consumption before room handlers.
- Verification: inventory smoke coverage includes navigation passthrough, ordinary outside dismissal after unregistration and precedence of inventory controls over navigation. Tests are authored, not yet executed in Unity; all-room visual/back-navigation QA remains pending.
- `InventoryManager` is a persistent shared runtime service created through `InventoryManager.GetOrCreate()`.
- It implements `IInventoryAccess` and `IInventoryCombinationPolicyRegistry`.
- It owns item storage, inventory window UI, card/detail UI, item preview UI, hover text, selected item state, item drag ghost, item release events, item replacement/removal, and registered combination policies.
- `ItemActivated` is compatibility-routed to `ItemUsed`; rooms subscribe to `ItemActivated` for item-use behavior.
- Current enum values include room-specific items from multiple rooms. `Film = 23` was added during the ROOM4 merge while preserving existing ROOM2/ROOM3 enum values.

Room use:

- ROOM1 image UI directly collects required USB/photo fragments without choices; the phone is field-use-only and never acquired. Newspaper is field-read-only and closes to the desk on an outside-paper click. Empty drawers can close/reopen after collection without respawning items. ROOM1 hints/result bars and hotspot emphasis are hidden. Full contract: [ROOM1_CURRENT_SNAPSHOT.md](ROOM1_CURRENT_SNAPSHOT.md).
- ROOM2 directly acquires field items, opens the enlarger workspace on click (retaining `확대한다`), and attempts completion after unlock feedback with both photo-piece prerequisites intact. Guide text is hidden; drawer targets and keypad entry align to their artwork. [ROOM2 QA amendment](ROOM2_SPEC.md#current-qa-interaction-amendment).
- ROOM4 uses inventory for `Film`; using/activating film attempts to place it on the map when the current view is the map.
- ROOM5 uses inventory for interview record, betrayal note, and student ID.

Known structural note:

- `InventoryManager` is intentionally over-broad right now because it is carrying shared UI, item state, card actions, drag behavior, and combination behavior. Do not split before full-room behavior is verified.

### Timer

Primary files:

- `Assets/Scripts/RoomCountdownTimer.cs`
- `Assets/Scripts/RoomTimerSettings.cs`
- `Assets/Resources/RoomTimerSettings.asset`

Current behavior:

- Timer uses static shared session state: `SharedTotalSeconds`, `SharedRemainingSeconds`, `HasSharedSession`.
- Default/current participating-room duration is one hour (`60f * 60f`).
- `Initialize(duration, onExpired, onRetry)` creates per-room timer UI but synchronizes against the shared static remaining time.
- `DeductSeconds(seconds)` deducts from shared remaining time and flashes timer text red.
- Shared time uses realtime elapsed time, not only active room Update ticks, so scene loading and gaps between timer components count too. Reading remaining time synchronizes the clock.
- `StopTimer()` freezes the shared session without resetting it; ordinary room clears must not call it. ROOM5 ending still stops the session.
- Expiration closes inventory, shows a retry overlay, invokes the room expiration callback, and retry resets the shared session before reloading.

Room use:

- ROOM1 starts the one-hour session; its clear screen and ROOM2 transition do not pause it.
- ROOM3 exit requires the calendar-unlocked door AND current inventory ownership of `StudentInterviewNotice`. The next-room button rechecks ownership. Exiting does not consume the notice; the same inventory carries it into ROOM4. Missing carryover uses the indirect blocked-exit message, not a named-item hint.
- ROOM2/3 runtime bootstrap no longer auto-seeds missing carryover, even when inventory is empty or the scene is started directly. Only actually acquired, unconsumed items persist from previous rooms. Legacy seeding helpers are compiled only for explicit editor tests and are not called during normal play.
- ROOM2 bootstrap creates a timer UI using the existing session, without resetting remaining time. Expiration blocks room UI actions, drag-target use and transition. Retry resets the clock through the timer retry handler and returns to HorrorRoom with a new inventory.
- ROOM4 continues the shared timer through its clear screen and ROOM5 transition.
- ROOM4/5 are empty scene shells: their controllers now subscribe to `SceneManager.sceneLoaded` at runtime startup and initialize on each matching single-scene entry, not just the initial Play scene. The ROOM3→4 failure investigation found an actual ROOM4 load in Editor.log but no serialized controller in that scene. Existing duplicate-controller checks remain. The same entry fix applies to ROOM4→5; visual regression verification is pending.
- Understood as (user correction): ROOM1→2 is the transition UX reference for ROOM2→3, ROOM3→4 and ROOM4→5. Unlock alone is not scene loading: use the exit, see `N번 방 탈출 성공`, then click `N+1번 방으로`. ROOM2/3 immediate loads are removed. ROOM4 now separates door unlock from exit use. ROOM2/3/4 use `RoomExitOverlay` with ROOM1's colors, layout and explicit next-room button. No clear-screen timeout or automatic advance; room input is blocked behind it, inventory is preserved, and time keeps running. Unity transition QA remains pending.
- Scene-entry click-through guard: after a single-scene load, `SceneEntryInputGuard` blocks UI raycasts and manual room/inventory pointer polling for at least 0.2 realtime seconds AND until a neutral pointer frame (not held, pressed or released) occurs. No sleep or time-scale pause is used. This addresses the reported ROOM2→3 transition gesture reaching the destination, but that exact symptom still needs Play Mode reproduction/verification. QA: hold the next-room button through loading, release over a destination hotspot, confirm nothing opens, then click freshly and confirm normal interaction. Repeat ROOM1→2 and ROOM3→4→5.
- ROOM3 bootstrap now also displays the shared timer. On exit use it shows the ROOM1-style completion screen; only its next-room button loads ROOM4 once if the scene is available and time remains, without recreating inventory. Its timer expiration disables room raycast interaction; calendar/exit and view input reject expired sessions. End-to-end transition and expiration checks remain pending.
- ROOM5 starts/continues the one-hour shared timer, deducts one minute for wrong desk moves, and stops it when ending begins.

Known behavior/spec tension:

- `SHARED_TIMER_SPEC.md` and `SHARED_SYSTEM_INTEGRATION_SPEC.md` freeze an older per-room 12-minute baseline. The user subsequently confirmed a single one-hour budget for all rooms, continuing during room clears and transitions. This snapshot records that updated policy and the current partial integration; the older frozen timer docs are historical, not the current timer authority. End-to-end timer behavior remains untested.

## Deferred ROOM3 / ROOM4 presentation follow-up

- 사용자 요청: ROOM3도 ROOM1/2와 같이 퇴장 전 미충족 조건에서 구체적인 아이템·해법을 알려주지 않고, “아직 확인하지 않은 게 있는 것 같다.” 정도의 간접 문구를 사용한다. 상단 방/화면 제목도 숨기는 방향이다.
- 1차 코드 반영: ROOM3 상단 제목과 개발용 전환 완료 문구 제거, 닫힌 출구 안내를 간접 문구로 변경. ROOM4 상단 제목과 필름 적용 후 정답을 반복하는 안내 문구 제거.
- 실제 화면 QA는 아직 남아 있다. 이번 코드 반영을 ROOM4 플레이 완료로 간주하지 않는다.

## Refactor boundary

Do not refactor yet. First verify this snapshot against actual play:

1. Compile with all merged scripts.
2. Confirm scene build order and scene names: `HorrorRoom` → `Room2` → `Room3` → `Room4` → `Room5`.
3. Play the critical path for ROOM1, ROOM4, and ROOM5.
4. Fix only behavior blockers discovered in playtest.
5. After behavior is confirmed, refactor ROOM1 toward the ROOM2/ROOM3 style by extracting flows from `Room1ImageNavigationUI` and keeping controller rules stable.
