# Web QA builds

## Prepared QA package — 1.0.3 (20260910-192232)

The user approved the transparent gauge and requested a commit and deployment preparation. Version 1.0.3 includes the ROOM2/4 replacement images and fixed red-to-dark-red timer gradient described in [PRESENTATION_RELEASE.md](PRESENTATION_RELEASE.md).

- Source commit: `f9cb43ba82c0adc01b9e0e3d0f4587587c415f91`. Only the version bump from `ProjectSettings.asset` was staged; pre-existing package, renderer and shared-style changes remain local and are captured in `source-local.diff` and source manifests alongside the build.
- Unity 6000.3.23f1 passed `RoomTimerGradientBatchTest` and `Room2PlayerNavigationViewTest`. The latter's stale drawer-crop and pre-auto-exit message expectations were updated to match already-committed behavior, without modifying that runtime logic. Initial failed logs and the final passing log are retained.
- Fresh WebGL build succeeded in `00:07:05.5627792` with all five enabled scenes and saving disabled. Build source files under Assets/Packages/ProjectSettings were unchanged during the build (733 files checked).
- ZIP: `C:/Users/Minju/Downloads/GhostGame-v1.0.3-WebGL-20260910-192232.zip` — 171,837,900 bytes; 19 files; 235,387,230 bytes extracted.
- ZIP SHA-256: `b508685fb381f83b6fc86c8c6ddb9cb9716a13eef81e2f00042611aac1e8c1ce`.
- Player and evidence: `C:/Users/Minju/AppData/Local/GhostGameQA/release-1.0.3-20260910-192232`. Includes test/build logs, summary, source commit/local diff/manifests, untracked source copies and package-verification records.
- Packaging checks passed: root index declares 1.0.3, ZIP CRC and every archived file hash match the player, flashback and bundled font notice match source, and the previous 1.0.2 ZIP is unchanged. The temporary `.zip.partial` is absent.
- After packaging, the trailing-space-only reserialization of `Room2ImageLibrary.asset` caused by its test builder was reverted. Sprite bindings were unchanged.
- No push or upload performed. Full ROOM1–5 playthrough and hosted browser/audio/video QA were not run for this package. The user approved the gauge composite preview; that is not a browser screenshot.
- No tool installation or extra project copy was needed. The existing Unity cache is retained; temporary release scripts were removed. The player, ZIP and evidence are intentional retained outputs outside the project.

## Prepared QA package — 1.0.2 (20260908-152017)

Understood as: commit the user-accepted current gameplay/presentation, retain but disable saving, and prepare the next WebGL release. Preserve the original 1.0.1 ZIP; do not push/upload or force-close Unity.

- Version bumped to `1.0.2`. Scope: ROOM2 drawer/photo normalization, ROOM3 field-only notice and transparent ROOM3/5 hotspots, ROOM5 bottom note-acquisition instruction, start/time-over artwork, police + one-shot knock + flashback + credits. See [PRESENTATION_RELEASE.md](PRESENTATION_RELEASE.md).
- The user reports the current Unity flow appears to work. That is not a complete hosted-WebGL or all-room regression result. Staged-index runtime/editor and non-editor conditional C# compilation passed using the installed Unity assemblies; this also checks the excluded shared-style source does not create a compile dependency. Latest execution testing remains user-owned.
- Builder now refuses an enabled save service or missing presentation media, and verifies the exported flashback's SHA-256 before declaring success. Actual browser audio/video playback still needs validation.
- User closed Unity. Unity 6000.3.23f1 WebGL build succeeded in 5m12.53s and exited with code 0. All five enabled scenes are included; saving is disabled. No upload or push performed.
- ZIP: `C:/Users/Minju/Downloads/GhostGame-v1.0.2-WebGL-20260908-152017.zip` — 172,093,354 bytes; 19 files; 237,722,707 bytes extracted. SHA-256: `cf22f7b3438e577d60e4fa4f364eac44b5f05f8c1ff50021db5c055bdf82da58`.
- Evidence/player root: `C:/Users/Minju/AppData/Local/GhostGameQA/release-1.0.2-20260908-152017`. Includes build log/summary, package manifest, HEAD/status/local diff, source-before/after hashes and previous ZIP hash. Build source is `d5e9bca6861260679edf7f5f70428585cb9e2a34` plus captured local Unity/URP/package/shared-style differences, not a clean checkout. No files under Assets/Packages/ProjectSettings changed during the build. Incoming originals and IDE files remain uncommitted.
- Packaging gates PASS: root index version 1.0.2, bundled font notice, exact flashback video hash, ZIP CRC, every archived file hash, unchanged original 1.0.1 ZIP. No temporary dependencies/server/project copy were created; the temporary `.zip.partial` is absent after finalization. Build player and evidence are intentionally retained externally.
- Browser playback was not executed for this package. Local/hosted checks still need to cover start, retry/menu, police/knock timing, full video completion and credits; packaging checks do not prove these.
- Supplied start/time-over art still contains its original `VER 1.0.0` lettering. This is baked artwork, not the player/package version, and has not been silently repainted.


## Prepared QA package — 1.0.1 (20260908-010633)

Understood as: commit the accepted ROOM5 changes, bump the existing 1.0 player version to 1.0.1, then produce a fresh WebGL QA ZIP; do not push, upload, overwrite an older package, or forcibly close the interactive editor.

- ROOM5 checkpoint: `b17609a`; includes enlarged scrollable record, UI event dispatch, hidden hint/result bars and corrected note/student-ID resource bindings. Earlier ROOM1–4 checkpoints are included in branch history.
- Player version: `1.0.1`. Product name is unchanged. Builder records the player version in its output summary.
- User closed Unity; fresh Unity 6000.3.23f1 WebGL build succeeded in 5m07s with all five enabled scenes. `index.html` declares version `1.0.1`. Source note/student-ID resource bindings match the new runtime artwork.
- Upload ZIP: `C:/Users/Minju/Downloads/GhostGame-v1.0.1-WebGL-20260908-010633.zip` — 155,559,241 bytes; 18 files, 220,019,187 bytes extracted. Root `index.html`, ZIP CRC and every archived file's SHA-256 were verified against the player output. The bundled font notice matches source.
- Archive SHA-256: `a5f44402d0fba9a1ee8f312988bdfada2086361bc1ce2dc86a94f7912b13e4e8`.
- Retained output/evidence root: `C:/Users/Minju/AppData/Local/GhostGameQA/release-1.0.1-20260908-010633`. Includes player, build log/summary, package manifest and before/after source hashes. No project copy, dependency installation or temporary server was created. Existing Unity build caches and previous release packages were retained. No upload or push performed.
- Build source is `c74ae06` plus captured local settings/package/shared-style differences, not a clean release checkout. `source-local.diff`, `source-status.txt` and source manifests record provenance. No files under Assets/Packages/ProjectSettings changed during the build. Unrelated settings, incoming originals and IDE files remain uncommitted.
- This is QA distribution preparation, not full ROOM1–5 regression acceptance. Source compilation passed; revised Play Mode tests and current-version browser playthrough have not run.

Historical build evidence below does not apply to the new 1.0.1 player.

## Prepared itch.io QA package — 20260907-155438

- Upload artifact: `C:/Users/Minju/Downloads/GhostGame-qa-20260907-155438.zip` (157,634,496 bytes, about 157.6 MB). Prepared only; not uploaded or published.
- Output/evidence root: `C:/Users/Minju/AppData/Local/GhostGameQA/itch-qa-20260907-155438`. Includes successful build report, passing non-default timing wiring test, browser screenshots/report and `package-manifest.json` with archive/source hashes.
- Includes latest working-tree content and shared settings: unlock wait 1 second, result messages 2 seconds. Source is `e235cad` plus uncommitted changes, not a committed release tag.
- ZIP integrity check passed. `index.html` is at the archive root; all 18 player files are included, including the matching font copyright/license notice. Extracted total 222,303,292 bytes; largest file `Build/WebGL.data` is 170,852,044 bytes. File counts/path lengths/sizes satisfy the default limits documented at https://itch.io/docs/creators/html5 at inspection.
- SHA-256: `95935b7a1ac146f4fbad01b357d33113b21cab93197fff93fdfeaea4521d3e00`.
- Local headless Chromium loaded ROOM1 with Korean text. Screenshots show the startup message present and then absent after a further 2.3-second observation interval, while the permanent hint remains; inventory opening/header dragging were also observed. No console/page errors or failed requests were captured. This is not full-room gameplay or hosted-itch validation.
- ROOM4 film-drop smoke failure remains unresolved; do not imply that packaging clears it. User-driven QA remains necessary before sharing with testers.
- Temporary browser tool installation and port-8766 test server were removed/stopped. The existing port-8765 crossword preview was left untouched so ongoing user play was not interrupted; it is older than this ZIP.
- Suggested itch page setup: HTML Game, In development, No payments, browser-play ZIP, Embed in page at 960×640, Click to play enabled, Mobile friendly off. Save Draft for owner preview first; tester access/public visibility is a separate step.

## Current local preview: crossword closeup and terminal ending

- `http://localhost:8765` now serves `C:/Users/Minju/AppData/Local/GhostGameQA/webgl-20260907-crossword-01/WebGL`. Rebuild succeeded and local HTTP returned 200. Reload with Ctrl+F5. The previous font-preview server was verified and stopped; its build/screenshots remain retained as evidence.
- Added ROOM4 newspaper-two crossword closeup and Back returning to that newspaper; kept wall/memo art and existing hints. ROOM5 now hides room-view name tags, shows `마지막 진실` instead of the numbered ending heading, and ends on `게임 클리어` without restart.
- `room5-smoke.log`: updated Unity Play Mode smoke PASS, including terminal no-reset and hidden-tag assertions.
- `room4-smoke.log`: crossword action/artwork and back-stack assertions passed before the suite failed at `Film-on-map state was not saved`. Do not treat the whole ROOM4 suite as passed or assume a cause without diagnosis.
- This revision has not had a browser playthrough of rooms 4/5. Earlier font/startup browser evidence below applies to the previous build, not a full visual validation of the new content.
- Current server PID/log are in this root. Restart command: `python3 -m http.server 8765 --bind 127.0.0.1 --directory /mnt/c/Users/Minju/AppData/Local/GhostGameQA/webgl-20260907-crossword-01/WebGL`.

## Previous follow-up: Korean font and common drag skin

- Previous output/log root: `C:/Users/Minju/AppData/Local/GhostGameQA/webgl-20260907-font-01`; this build was served for the font smoke, and is now superseded by the preview above. The previous server was stopped after checking its command line; its stale PID file was removed. The previous build/evidence remains retained as the failing font reference.
- Rebuild succeeded. Browser screenshot review confirmed Korean ROOM1 instructions/hints, `남은 시간` and `인벤토리 · 드래그 이동`, previously absent. Inventory opening, window dragging and timer decrease remain visible; no page/console errors or failed requests were captured in this smoke.
- Nanum Gothic Regular is bundled and all OS-font factories in runtime scripts were replaced by `GameUiFont`. Its OFL copyright/license notice was fetched from the built HTTP output and matched against the source notice. Provenance and license: [FONTS.md](FONTS.md).
- Drag-target highlighting now uses the same sliced box skin and RGB as field hover. Both default to alpha 0.25; only drag alpha can be adjusted independently. Actual item-target dragging and all-room contrast still require play QA; this smoke exercised inventory-window dragging, not item drops.
- `browser-report.json`, the three `browser-*.png` screenshots and `browser-check.cjs` are retained in the current root. Temporary browser dependencies were removed and the headless browser closed; the local-only HTTP preview remains running. Reload with Ctrl+F5 to fetch the updated build.

## Initial build result and limits (historical)

- Built the existing project with Unity **6000.3.23f1** and its installed Web build support. `ProjectVersion.txt` previously recorded 6000.3.22f1; Unity updated it on this run.
- Build succeeded with all five enabled scenes, starting at `Assets/Scenes/HorrorRoom.unity`. BuildReport: 219,483,704 bytes, 13 minutes 25 seconds (not including the initial editor/platform import).
- Local headless Chromium with software WebGL2 loaded ROOM1. Screenshot review confirmed inventory-button activation, inventory-window header dragging and timer decrease (59:58 → 59:56 → 59:54). No page errors, console errors or failed requests were captured during this smoke.
- **Blocker: Korean UI text is absent in the browser captures.** Runtime UI currently creates fonts from OS font names, and no `.ttf`/`.otf` files were found under Assets. A redistributable Korean font must be bundled and connected, then the same browser check repeated. Do not copy a Windows system font into the distribution without checking its redistribution rights.
- This is not item drag/drop verification, full ROOM1→5 progression, expiration testing, audio QA, visual parity acceptance, or public deployment readiness. The default web template still shows the product label `My project`.

## Build mechanism

`Assets/Editor/WebQaBuild.cs` exposes `WebQaBuild.Run` for batch invocation:

```text
Unity.exe -batchmode -quit
  -projectPath C:/Users/Minju/dev/Ghost_Game
  -buildTarget WebGL
  -executeMethod WebQaBuild.Run
  -qaOutputRoot <new absolute output directory outside the project>
  -logFile <build log path>
```

Use the editor under `C:/Program Files/Unity/Hub/Editor/6000.3.23f1/Editor/`. Close the project's interactive editor before a batch build. Do not create another full project copy.

The builder refuses an existing `WebGL` output subdirectory, checks the enabled scene list and platform support, and removes its partial player output on a failed build. First-pass QA output is uncompressed for simple HTTP hosting; compression settings are restored after the build. Build success does not imply browser correctness.

## Retained local artifacts and preview

- Output/log root: `C:/Users/Minju/AppData/Local/GhostGameQA/webgl-20260903-01`. The folder suffix is a run identifier; the browser receipt timestamp is 2026-09-07 UTC.
- `WebGL/`: retained QA player, including `index.html`, `Build/` and `TemplateData/`.
- `build.log`, `build-summary.txt`: Unity build evidence.
- `browser-report.json`, `browser-initial.png`, `browser-inventory.png`, `browser-inventory-drag.png`, `browser-check.cjs`: browser smoke evidence and probe source.
- Local-only preview: `http://localhost:8765`, served from the output's `WebGL` directory by a WSL Python HTTP server. Windows HTTP access returned 200 during this run. This is not a shareable internet URL.
- `http.pid` and `http.log` identify the preview process and requests. Before stopping it later, verify that the recorded PID still belongs to this server; do not blindly kill a reused PID.
- To restart locally from WSL, run `python3 -m http.server 8765 --bind 127.0.0.1 --directory /mnt/c/Users/Minju/AppData/Local/GhostGameQA/webgl-20260903-01/WebGL`.

Build outputs and evidence are deliberately retained for QA. Existing Unity Library/Bee caches now include WebGL artifacts; no project clone was made and old temporary projects were not deleted. The temporary Playwright dependency installation and headless browser process used for this smoke were removed/closed after testing; the local preview server remains available for the user.

## Source changes to review before committing

Unity migrated some URP settings and package versions when opening the project in 6000.3.23f1. WebGL batching and platform-specific defines were also recorded. These changes are not a room refactor. Preserve the user's pre-existing Standalone `SENTIS_ANALYTICS_ENABLED` define and unrelated settings/artwork. No commit, push or upload is part of this build checkpoint.

## Next action

Internally play the current web build through all rooms: Korean text and layout beyond the sampled screens, item drag/drop and matching target cues, shared inventory/time and ending. The default product title still needs attention. Existing ROOM4 wall/memo art is now intentionally retained for QA; verify the newly added crossword closeup instead. Only after blockers are addressed should a QA distribution ZIP/page be prepared.
