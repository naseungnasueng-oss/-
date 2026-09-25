# Presentation release 1.0.3

Version 1.0.3 contains the accepted ROOM2/4 replacement art and transparent gauge overlay. The previous ROOM4 transparent hotspots and police → full knock clip → flashback → start-screen ending are included. Package status and evidence are recorded in [WEB_QA_BUILD.md](WEB_QA_BUILD.md).

## Accepted artwork

- ROOM2: replace drained sink before and after photo collection, plus photo 3 before enlargement.
- ROOM4: replace the left wall, door closeup and the left newspaper (`newspaperOne`). Preserve sprite GUIDs and internal IDs; resize the door/newspaper sprite rectangles to their new full frames. The supplied right-wall notepad is excluded.
- Gauge artwork uses alpha 20%. Its compartment fill pixels are transparent; the frame, dividers and skull remain unchanged. The original PNG is retained at `ArtSource/게이지바/게이지바_원본.png`; the adjacent preview is a composite, not a game capture.
- The fill runs left to right through `#D83A32`, `#8B171D` at the midpoint and `#35070C`, uniformly at alpha 75%. Gradient positions stay fixed to the full track as the remaining bar shrinks from the left. The panel remains transparent.

## Accepted flow

- Preserve the save implementation and existing stored data, but do not install the save service in this release. No Save button, Continue menu, startup read, or automatic write. Explicit isolated save-test entry points remain available to tests only.
- Built game: supplied start artwork → Game Start → ROOM1, empty inventory, fresh one-hour run. Landing/loading do not charge time. Direct Unity scene Play stays in the selected room.
- Any room at zero time: supplied TIME OVER artwork. Retry starts the entire run from ROOM1; Main returns to the landing view. Old puzzle/inventory input is blocked.
- ROOM5 ID confirmation (not item acquisition): stop time, replace the gauge character with the six-frame police sheet, hold the current view for the complete one-shot knock clip (full clip duration and playback completion), then play the supplied flashback MP4. Natural video completion returns to the start screen. There is no fixed 1.5-second transition or separate terminal credits screen.
- Video preparation/start failure displays retry and an explicit return-to-start action, rather than silently treating failure as successful completion. Returning to the start screen does not automatically start another run.
- Door-knock audio starts once, immediately when ID confirmation changes the gauge person to police. It is non-positional, not looping, and does not play on scene startup. The video waits until the one-shot has ended; repeated ending triggers do not restart it. Returning to the landing view or destroying the presentation stops it.

## Ownership and files

`Assets/Scripts/Shared/GamePresentation.cs` owns landing, loading, time-over, police reveal, video and error states; it never accesses PlayerPrefs. `SceneEntryInputGuard` blocks underlying input; `RoomCountdownTimer` pauses for landing/loading and accepts a character-frame override. ROOM5 starts the ending once through its existing ID/time prerequisites. `GameSaveSession.FeatureEnabled` is false.

Originals in `ArtSource` are preserved. Copies were checked byte-for-byte with SHA-256:

| Source | Runtime copy | SHA-256 |
|---|---|---|
| `Game start.png` | `Assets/Resources/Presentation/game-start.png` | `0dbb920eed395ba776be51e170b419993f822a6c03e1e6547c43343c3ccc2bee` |
| `timeover.png` | `Assets/Resources/Presentation/time-over.png` | `b96fe20e61e45b19d462f93a997f240dfd09fe8eb8c50100f5aa263bcfcaaf65` |
| `경찰.png` | `Assets/Resources/Presentation/police.png` | `d879880cd4c7fafa531b6b8f3b80c01ff57a44889fb7386bdd892fba6992bce3` |
| `플래시백.mp4` | `Assets/StreamingAssets/Presentation/flashback.mp4` | `48930cfb45feec04e889cff5ae53c128dd5be24199a1985f2a34cf379a4cd4a1` |
| `문 쾅쾅쾅.mp3` | `Assets/Resources/Presentation/door-knocks.mp3` | `9afcc4ba370b169b30e60931dff3089751cf8a761681dad83320526f996661d0` |

Start/time-over images already contain labels and arrows. Transparent hit regions follow the aspect-fitted artwork; no duplicate labels are drawn. Their embedded `VER 1.0.0` marking is unchanged artwork, not a new bundle-version claim. In WebGL/editor, Exit gives a close-window instruction; native players request application quit.

## User-run Unity checks

Restart Play to pick up the current artwork and timer. These manual checks cover presentation beyond the automated checks below.

1. Play a room directly: no landing/save/Continue interruption.
2. Play Mode menu `Tools > Game > Show Release Start Screen (Play Mode)`: verify artwork and Game Start click; verify ROOM1 reset and full time. The old save-preview menu is not this release menu.
3. Expire the timer: verify new artwork, Retry from ROOM1 and Main back to title. Neither button may activate the room behind it.
4. ROOM5: ID confirmation changes the gauge person to police and immediately plays the knocks once; verify audible volume and the transition into video. The police appears before video; no old text pages/ID overlay obscure it. The timer remains stopped and repeated clicks do not restart the sequence.
5. Confirm the current police/room view remains throughout the knocks without video overlap. Watch the actual video to completion: the supplied start screen returns; Game Start resets the entire run. Verify local video/audio behavior and small/wide Game-view layouts. Check actual hosted WebGL video behavior separately before distributing.

## Verification boundary

Unity 6000.3.23f1 compiled the current sources and passed `RoomTimerGradientBatchTest` and `Room2PlayerNavigationViewTest`. The gradient test checks fixed colors, uniform opacity, midpoint position, remaining width and an empty expired bar. The ROOM2 test covers contextual navigation, acquisition, sink states, all four enlargement results and exit eligibility. Its stale drawer-crop and pre-auto-exit message expectations were aligned with already-committed behavior; runtime logic was not changed for those checks.

The user approved the edited gauge preview. Automated checks are not a complete ROOM1–5 playthrough or hosted browser/audio/video verification. Build and archive verification are recorded separately in [WEB_QA_BUILD.md](WEB_QA_BUILD.md).
