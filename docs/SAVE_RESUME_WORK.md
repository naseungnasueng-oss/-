# Save/resume implementation work (unreleased)

**Release override:** the current [presentation release](PRESENTATION_RELEASE.md) keeps this implementation but disables normal installation (`GameSaveSession.FeatureEnabled == false`). No saving/loading UI or storage I/O is enabled in normal play; existing saves remain untouched. The manual-save design below is retained for a future enabled version.

Retained save-enabled contract: in the built game, a full-screen landing view offers New Game / Continue (disabled when no valid save exists). During gameplay, only the explicit Save button writes current room, acquired/consumed items, puzzle progress and remaining time. No automatic progress/timer/focus/quit writes. New Game resets the live run without deleting/replacing the previous checkpoint until the player explicitly saves. Offline time does not count. Inspection windows, scroll offsets and unfinished lock input reset. Keep the distributed 1.0.1 ZIP unchanged; do not upload or push.

Unity scene Play starts directly in the currently open room, with no forced landing or save restoration. Its Save button uses a separate editor-test key, not an extra player-visible slot. To test the landing/Continue flow deliberately in Play Mode, use `Tools > Game > Show Start Screen (Play Mode)`; this uses that same editor-test checkpoint. Built players always begin with the landing view and have one checkpoint. Room transitions do not reopen the landing view.

Current manual-save/landing revision: compilation checked separately; revised Play Mode assertions are left for the user's execution testing. Earlier automatic-save browser receipts below do not validate this changed UI/write policy.

Implementation gates:
- Restore state directly, never replay pickup/use/penalty actions.
- Save inventory and room state together after gameplay updates; do not overwrite a save while deciding whether to continue or while restoring.
- Preserve consumed items, fixed letters, cleared map examples, desk penalty history, expiration and ending state.
- Validate schema, room and bounded values before applying; keep a previous valid save for recovery. Report storage failures instead of claiming success.
- Use the same serialized game data in Editor and WebGL. Editor tests do not establish browser durability; refresh/tab-close testing of the actual WebGL build remains required.
- Isolate automated storage tests from the player's keys. No dependency installation or project clone. Temporary compiler output belongs in the user Temp directory and must be removed on success/failure; retained QA build/test evidence belongs in the external GhostGameQA directory.

## Implemented (uncommitted / not distributed)

- `GameSaveData` is one whole-run payload: current scene, visited-room mask, room snapshots, inventory IDs and remaining seconds. The mask is required because Unity JSON can materialize unvisited/null room objects as empty placeholders. Unvisited placeholders must not be validated as completed room snapshots.
- On built-game startup, `GameSaveSession` shows an opaque full-screen landing view, rather than a popup over visible gameplay. Editor room tests bypass it; the explicit editor menu can preview it. Gameplay input and the clock are gated while selecting/loading. LateUpdate keeps visited-room progress together in memory, but storage writes require a Save-button request. The bottom-left Save control consumes its pointer gesture without stopping the clock; item/panel dragging does not activate it. No save occurs on focus loss, pause or quit. Unsaved changes are intentionally discarded on restart.
- `GameSaveStore` uses PlayerPrefs with a schema-checked, checksummed JSON envelope and a previous valid backup. Editor and WebGL share the codec/state adapters, not the same physical storage. Snapshot restoration does not fire item-use/acquisition/combination events or replay desk penalties.
- ROOM1 registers for scene reloads as well as initial startup. Without that correction, WebGL Continue could restore the raw original scene without its runtime image UI/controller.
- Room progress includes fixed/revealed letters, consumed items, retired locks, cleared map/example state and desk history. Unfinished lock/date input, inspection/navigation windows, scroll positions and ROOM2's unlocked drawer's visual open/closed state are transient. The latter's acquired photo flag is persisted.
- Inventory item enum IDs and save field meanings must remain stable; incompatible future changes need a new schema/migration. The existing 1.0.1 distributed player has no save feature and cannot retroactively export its in-memory progress.

## Executed verification — previous automatic-save revision

Evidence root: `C:/Users/Minju/AppData/Local/GhostGameQA/save-tests-20260908-01`.

- `rules-store-04.log`: PASS — rule snapshots, whole-run JSON round trip, unvisited-room handling, schema/duplicate-item rejection and corruption recovery using an isolated test key.
- `play-resume-08.log`: PASS — actual scene reloads for all five adapters; ROOM5 automatic save and continuation preserve inventory/time and do not repeat a desk penalty. Additional controlled fixtures exercise ROOM1 bootstrap, ROOM2 consumed USB/reveals, ROOM3 consumed tissue/retired locks, ROOM4 used film and cleared-map non-reseeding, expiration and terminal ending. These fixture reloads are not a full puzzle playthrough.
- The earlier failing automatic-save tests exposed the null-room JSON issue; the first browser probe exposed the ROOM1 re-entry bootstrap issue. Both were corrected before the passing runs. Some Unity batch imports stalled with a duplicate Bee-backend error; only the owned blocked batch processes were stopped/retried, not an interactive editor.

Browser evidence: `C:/Users/Minju/AppData/Local/GhostGameQA/save-web-20260908-02`.

- Internal WebGL build succeeded. It still carries the development project's 1.0.1 product label, but is NOT the previously distributed 1.0.1 ZIP or a new release package.
- `browser-report.json`: PASS — actual mouse-driven USB pickup, verified game payload in IndexedDB, refresh/Continue, unchanged stored time during menu wait, resumed countdown, entire browser process close/reopen with an isolated persistent profile, and New Game reset. No page/console errors were captured. Screenshots of continuation and reset were visually checked.
- Earlier `save-web-20260908-01` is retained failing-browser evidence, not the recommended test build.
- Remaining acceptance: hosted itch iframe storage behavior, all-room browser puzzle playthrough, quota/storage-denial handling and cross-version/host-URL migration. Do not infer these from the local Chromium smoke.

Temporary browser dependencies/profile were removed and absence checked; the owned port-8767 server was stopped. Test scripts/reports/screenshots/build logs/player output are intentionally retained in the external QA folders. The existing port-8765 preview was untouched. The distributed ZIP SHA-256 was rechecked as unchanged: `a5f44402d0fba9a1ee8f312988bdfada2086361bc1ce2dc86a94f7912b13e4e8`. No Unity process remained at closure. No commit, push or upload was performed in this implementation pass.
