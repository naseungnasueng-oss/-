# Field evidence QA corrections (unreleased)

Understood as: inspect ROOM2's reported broken drawer/photo rendering, fix it if the cause can be addressed without inventing artwork; normalize left/right photo-fragment presentation; make ROOM3's notice field-only; make ROOM3/5 field hotspots invisible. Preserve ROOM5's 15-second required instruction and existing saves/release ZIP.

## ROOM2 findings and correction

- Drawer and inventory right-photo PNGs have identical SHA-256 `8b925d52ae54a7719df64c5bfdaa9e3c2aa0f7d8baba802f4c66ce960963b35f`. Both match their corresponding files under `ArtSource/2번방/누끼/`. Different filenames do not mean different pictures.
- The image itself is visibly pixelated. No higher-detail alternative was found among those supplied originals. Recovering detail requires replacement artwork; no synthetic detail or artwork overwrite was attempted.
- The opened drawer was a 390×260 crop stretched into a 480×320 patch over differently framed closed artwork. Background selection now uses the complete opened/closed sprites; the patch is disabled. Original images and click targets are retained. Actual visual/click alignment still needs user QA.
- The right-fragment inventory image had a large transparent canvas; the left was already tightly sliced. Runtime-only cropping removes the right padding for slot and preview presentation. Originals remain unchanged. Save restoration uses the same normalization. Created sprites are released with the inventory.

## ROOM3 field notice

- Clicking the notice in the unlocked/open drawer reads it directly, without acquisition or inventory-capacity dependency. After dismissing or closing/reopening the drawer, the notice remains visible and readable.
- The previous exit ownership prerequisite is now a reading prerequisite. `IsNoticeInspected` is persisted through the existing `notice` save field; legacy `IsNoticeAcquired` callers are a compatibility alias, not proof of ownership.
- Existing saves that already own a notice are not silently stripped of items; their inventory reading remains supported. Newly started/played acquisitions do not add a notice.
- Notice hotspot and drawer-close hotspot no longer overlap. The drawer never switches to the empty-notice artwork merely because the paper was read.

## Hotspots and ROOM5 exception

- ROOM3 field buttons have no tint transition/hover fill. ROOM5 hover retains only each region's normal color (clear for field regions). Actual control backgrounds and separate inventory drop affordances are not removed.
- Current contract (see `ROOM5_SPEC.md`): successful note acquisition shows both desk/penalty sentences at the bottom for 15 realtime seconds. Cabinet opening and restore do not trigger it. Text height/padding and best-fit were adjusted to avoid truncation. Runtime display is left for user QA.
- Historical diagnosis, not the current trigger contract: the earlier cabinet-opening instruction was hidden. An earlier correction removed the first-opening restriction and keeps the instruction above note inspection overlays. A further missing-instruction report exposed `FinishLockerUnlock → OpenDoyunLocker → CloseCabinet`: opening displayed the instruction, then closing the lock overlay immediately hid it. `CloseCabinet` now preserves an active instruction. The smoke regression now executes the real post-delay completion path with an open lock overlay, rather than only calling the controller's open method. Runtime/editor compilation passed; the revised regression and actual display remain for user-run Unity testing.

## Verification and boundaries

- Runtime/editor compilation passed.
- `C:/Users/Minju/AppData/Local/GhostGameQA/field-evidence-20260908-01/check-02.log`: focused Unity batch assertions PASS for repeated field notice reading, inventory-full independence, reading prerequisite/save state, full drawer background selection, and non-destructive photo cropping.
- Related ROOM3 controller/direction-notice/playable-path expectations were revised. These full suites and latest physical hover/visual behavior have not been run in this correction pass.
- Initial batch compilation hit the previously observed Bee duplicate-backend stall; only the owned blocked batch was stopped and retried. Compiler scratch was deleted on success/failure. No dependency install, release build, ZIP replacement, commit or push was performed.
