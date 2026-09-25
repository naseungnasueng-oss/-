# ROOM2 Asset Manifest

Canonical human-readable source for ROOM2 art requirements and runtime `Room2ImageLibrary` mappings. `Room2ImageLibraryBuilder` is the executable path-to-slot contract.

## Current delivery rules

- Preserve existing PNG paths and Unity GUIDs after import.
- New PNGs receive unique `.meta` GUIDs and must be committed with their `.meta` files.
- Keep puzzle text in runtime UI except where the delivered final enlargement artwork intentionally contains its own revealed mark/value.
- The four sink-photo inventory cards share the supplied `배수구 사진 아이템 이미지` inspection sprite. Their internal runtime identities remain distinct for enlargement results, but inventory art does not distinguish them.
- The enlarger uses two photo-specific visual stages:
  1. `room2_enlarger_photoN_loaded.png`: the supplied `확대전` image;
  2. `room2_enlarger_photoN_result.png`: the supplied `2차 확대` image and final result.
- The supplied `1차 확대` images are intentionally unused.
- Source-folder order is preserved: photo 1 reveals `Z=3`, photo 2 reveals `W=4`, photo 3 reveals `X=1`, and photo 4 reveals `Y=2`. The USB memo supplies the meaningful `Z → W → X → Y` order, yielding keypad code `3412`.
- Legacy shared slots `enlargerPhotoLoaded` and `enlargerPhotoMagnified` remain compatibility fallbacks only.
- A path marked **Missing** is a reserved future contract: leave its library field null and use the stated runtime fallback. Do not create placeholder PNGs at those paths.
- `IsReady` checks the minimum playable image set. `IsRoom2ReplacementArtComplete` additionally requires every non-legacy replacement slot described by its implementation; the three remaining raster gaps keep that stricter signal false.
- Follow the neighboring ROOM2 sprite import settings (single Sprite, no mipmaps, preserve aspect); do not invent per-file overrides without a visual requirement.

## Composition contracts

- `computerAreaDrawerClosed` is shown with the drawer closed. Opening the drawer selects the full `computerAreaDrawerOpen` frame; collecting its photo leaves that frame visible until the drawer is closed.
- `computerCloseup` is shown before USB use; `computerCloseupUsbInserted` replaces it after successful use, with the exact clue rendered inside the blue monitor area.
- `sinkCloseupPlugged`: sink before draining.
- `sinkCloseupDrainedWithPhotos`: full drained-sink frame before the four photos are collected.
- `sinkCloseupDrained`: full drained-sink frame after collection.
- `sinkWetPhotosLayer`: legacy exact-registration fallback used only if the supplied full drained-with-photos frame is absent.
- `enlargerReveal` and `enlargedEmptyFloorBase`: both fields must reference the same imported Sprite at `room2_enlarger_reveal.png`, serving as the empty enlargement workspace/base.
- A photo-specific loaded image takes priority over `enlargerPhotoLoaded`.
- A photo-specific result image takes priority over `enlargerPhotoMagnified` and the empty base. Because the delivered result raster already contains the revealed mark/value, the runtime mapping text overlay is suppressed when that raster is present; domain state still exposes the same mapping.
- `oldMetalHook` is shared by room-object and inventory/detail presentation.
- `rightOldPhotoFragmentDrawerLayer` and `rightOldPhotoFragmentInventoryInspection` belong to the ROOM2 right old-photo fragment, not the four sink photos.

## Runtime mapping table

| Canonical path | Library field | Status / use |
| --- | --- | --- |
| `Assets/Art/Room2/Views/Overview/room2_overview_hook_available.png` | `overviewHookAvailable` | Legacy full frame retained as source/reference. |
| `Assets/Art/Room2/Views/Overview/room2_overview_hook_collected.png` | `overviewHookCollected` | Playable overview base. |
| `Assets/Art/Room2/Layers/room2_old_metal_hook.png` | `oldMetalHook` | Delivered hook object and inventory/detail art. |
| `Assets/Art/Room2/Views/Computer/room2_computer_area_drawer_closed.png` | `computerAreaDrawerClosed` | Closed computer-area frame. |
| `Assets/Art/Room2/Views/Computer/room2_computer_area_drawer_open.png` | `computerAreaDrawerOpen` | Full computer-area frame while the drawer is open. |
| `Assets/Art/Room2/Layers/room2_right_old_photo_fragment_drawer.png` | `rightOldPhotoFragmentDrawerLayer` | Delivered right-fragment room layer. |
| `Assets/Art/Room2/Inventory/room2_right_old_photo_fragment_inspection.png` | `rightOldPhotoFragmentInventoryInspection` | Delivered right-fragment inventory/detail art. |
| `Assets/Art/Room2/Views/Computer/room2_computer_closeup.png` | `computerCloseup` | Computer closeup base. |
| `Assets/Art/Room2/Views/Computer/room2_computer_closeup_usb_inserted.png` | `computerCloseupUsbInserted` | Playable post-USB closeup; runtime clue text occupies its blue monitor area. |
| `Assets/Art/Room2/Layers/room2_computer_memo_screen_base.png` | `computerMemoScreenBase` | Missing; runtime memo UI remains the fallback. |
| `Assets/Art/Room2/Views/Sink/room2_sink_enlarger_area.png` | `sinkEnlargerArea` | Sink/enlarger route view. |
| `Assets/Art/Room2/Views/Sink/room2_sink_enlarger_area_closeup.png` | `sinkEnlargerAreaCloseup` | Enlarger route closeup. |
| `Assets/Art/Room2/Views/Sink/room2_sink_closeup_plugged.png` | `sinkCloseupPlugged` | Pre-drain sink. |
| `Assets/Art/Room2/Views/Sink/room2_sink_closeup_drained_with_photos.png` | `sinkCloseupDrainedWithPhotos` | Delivered drained sink before collection. |
| `Assets/Art/Room2/Views/Sink/room2_sink_closeup_drained.png` | `sinkCloseupDrained` | Delivered drained sink after collection. |
| `Assets/Art/Room2/Layers/room2_sink_wet_photos_layer.png` | `sinkWetPhotosLayer` | Legacy optional fallback layer. |
| `Assets/Art/Room2/Inventory/room2_sink_photo_shared_inspection.png` | `sinkPhoto1InventoryInspection`–`sinkPhoto4InventoryInspection` | One delivered drain-photo inspection sprite mapped to all four cards. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_closeup_before_use.png` | `enlargerCloseupBeforeUse` | Connected from `ArtSource/사진확대기.png`; closeup background and object hit/drop region updated. Visual QA pending. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_reveal.png` | `enlargerReveal`, `enlargedEmptyFloorBase` | Empty workspace and final fallback. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo1_loaded.png` | `enlargerPhoto1Loaded` | Delivered photo 1 `확대전`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo2_loaded.png` | `enlargerPhoto2Loaded` | Delivered photo 2 `확대전`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo3_loaded.png` | `enlargerPhoto3Loaded` | Delivered photo 3 `확대전`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo4_loaded.png` | `enlargerPhoto4Loaded` | Delivered photo 4 `확대전`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo1_result.png` | `enlargedPhoto1Result` | Delivered photo 1 final `2차 확대`; reveals `Z=3`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo2_result.png` | `enlargedPhoto2Result` | Delivered photo 2 final `2차 확대`; reveals `W=4`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo3_result.png` | `enlargedPhoto3Result` | Delivered photo 3 final `2차 확대`; reveals `X=1`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo4_result.png` | `enlargedPhoto4Result` | Delivered photo 4 final `2차 확대`; reveals `Y=2`. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo_loaded.png` | `enlargerPhotoLoaded` | Legacy shared loaded fallback. |
| `Assets/Art/Room2/Views/Sink/room2_enlarger_photo_magnified.png` | `enlargerPhotoMagnified` | Legacy shared final fallback. |
| `Assets/Art/Room2/Views/Door/room2_door_closeup.png` | `doorCloseup` | Locked door view. |
| `Assets/Art/Room2/Views/Door/room2_door_unlocked_closeup.png` | `doorUnlockedCloseup` | Missing; code-rendered unlocked indication remains fallback. |
| `Assets/Art/Room2/Views/Door/room2_keypad_closeup.png` | `keypadCloseup` | Keypad view. |

## Optional/future raster slots

- `room2_computer_memo_screen_base.png`
- `room2_door_unlocked_closeup.png`

These slots do not block the current playable baseline. The memo and unlocked-door runtime fallbacks are accepted; the dedicated enlarger closeup is now connected and awaits visual/input alignment QA. `IsRoom2ReplacementArtComplete` remains a stricter catalog-completeness signal rather than a ROOM2 completion gate.
