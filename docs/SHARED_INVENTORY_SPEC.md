# Shared Inventory Spec

## Scope and authority

This Spec freezes the adopted shared inventory contract for this integration phase. The observable baseline is the ROOM1 inventory snapshot at `06bfbc188c225a4990465856ebe553afdbf130fc`, `Assets/Scripts/InventoryManager.cs` blob `2e43c64288ae69aa8a9e08c75ca09538450953df`, cross-checked with the ROOM1/ROOM5 smoke tests from the same snapshot. Current ROOM2/3 code is evidence only for compatibility outcomes; it is not baseline behavior.

## Confirmed QA amendment: layout and initial placement

- Shared inventory uses **2 columns × 6 rows (12 slots)**. First opening places the window in the lower-right with a margin; later openings retain its moved position, clamping to the visible screen if needed. Room transitions preserve the persistent window's position. No save/relaunch persistence is implied.
- Keep draggable-header/close controls, capacity refusal, ownership and input protection. Do not silently discard items to fit the smaller capacity; full-run carryover capacity requires QA.
- ROOM1 now grants USB/photo fragments directly on field click and never grants phone ownership. Its phone remains field-use-only. This ROOM1 policy does not change shared inventory capabilities. ROOM2 subsequently adopted direct hook/drawer-photo/sink-photo pickups under its own [QA amendment](ROOM2_SPEC.md#current-qa-interaction-amendment); other rooms keep their existing policies.
- These confirmed amendments override conflicting historical baseline facts below. Current ROOM1 details: [ROOM1_CURRENT_SNAPSHOT.md](ROOM1_CURRENT_SNAPSHOT.md).

## Confirmed integration amendment: outside back navigation

This amendment takes precedence over the historical baseline for pointer dismissal in the integrated ROOM1–5 build. Current implementation details are in [SHARED_CURRENT_SNAPSHOT.md](SHARED_CURRENT_SNAPSHOT.md).

- With inventory open, one click on an available room Back control **outside** inventory navigates back while keeping inventory open.
- Inventory-covered controls cannot receive that click. Inventory buttons, window, cards/previews and active drag handling retain pointer priority.
- Ordinary outside clicks still close inventory and consume the gesture; they must not activate the room underneath.
- Only an available Back control qualifies. Hidden controls, room modals and blocked/expired sessions do not bypass input protection.
- This is within-room navigation, not a change to scene-transition, inventory ownership or item-consumption rules. A destroyed room view must no longer grant input exceptions.
- Acceptance: exercise each room's Back with inventory open; verify one-click navigation and retained inventory, an inventory-covered Back being blocked, ordinary outside dismissal, and no stale exception after room transition. Authored tests and compilation do not replace these play checks.

## Snapshot facts: historical baseline observable behavior

- A single `InventoryManager` instance is used. `GetOrCreate()` returns the existing instance or creates one, and the accepted instance persists across scene loads.
- The inventory UI is runtime-created in an overlay canvas. It exposes:
  - an always-available inventory button that toggles the inventory window;
  - an inventory window that starts closed;
  - an explicit close button (`X`) inside a draggable header;
  - `OpenInventory()`, `CloseInventory()`, `IsOpen`, `HasDraggableHeader`, and `HasCloseButton` as observable/testable controls.
- The baseline storage model is ordered ownership of item entries with 63 slots (7 columns x 9 rows).
- `AddItem(type, sprite)` appends an item when capacity remains, clears selection, refreshes UI, raises `ItemAdded(type)`, and returns `true`.
- If the inventory is full, `AddItem` logs a warning, changes no ownership state, raises no add event, and returns `false`.
- Duplicate item types are not rejected by inventory itself. Room callers may choose to check `HasItem` before adding.
- `HasItem(type)` reports whether at least one entry of that type exists.
- `RemoveItem(type)` removes all entries of that type, clears selection, refreshes UI, and returns `true` only when something was removed.
- Clicking an owned slot selects it. Clicking a different owned slot either combines a supported pair or moves selection to the new slot.
- The only built-in baseline combination is `TornNewspaper` + `TornPiece` in either order. It removes both entries, appends `WholeNewspaper`, clears selection/click tracking, raises `ItemCombined(WholeNewspaper)`, and requires a discoverable whole-newspaper sprite.
- If the whole-newspaper sprite cannot be found, combination refuses by logging an error and leaving item ownership unchanged.
- Double-clicking an owned slot opens that inventory entry and clears selection.
- Opening an entry closes the inventory window and closes any existing preview first.
- Opening `Phone` invokes `ItemActivated(Phone)` when a subscriber exists; it does not show the generic preview in that case.
- Opening any other entry shows a generic preview overlay. `PreviewItem(type)` opens the same preview for an owned item and returns `false` for an unowned item.
- The generic preview closes on the next pointer press. `CloseItemPreview()` explicitly hides it. `IsItemPreviewOpen` reports its visibility.
- Preview title uses the inventory label; if the sprite is missing, the preview still opens with a missing-image message and warning.
- Inventory pointer ownership is part of the contract: while preview is open, the inventory is being dragged, the inventory consumed the current frame, the pointer is over the inventory button, or the pointer is over an open inventory window, room hotspots must not also consume that pointer input.
- ROOM1/5 public surfaces relied on by snapshot tests and consumers are: `GetOrCreate`, `Instance`, `AddItem`, `HasItem`, `RemoveItem`, `OpenInventory`, `CloseInventory`, `PreviewItem`, `OpenItem`, `CloseItemPreview`, `IsOpen`, `IsItemPreviewOpen`, `HasDraggableHeader`, `HasCloseButton`, `ItemCount`, `ItemAdded`, `ItemCombined`, `ItemActivated`, and `IsPointerOverInventory`.

## Integration requirements for ROOM2/3 compatibility

- Preserve every baseline ROOM1 observable behavior above unless a future confirmed Spec explicitly changes it.
- Support ROOM2/3 drag-release routing for owned inventory items without confusing item dragging with dragging the inventory window/header.
- While an item drag is active or released, room click/hotspot handling must remain blocked for the same gesture, and the room may resolve the release against room-owned drop targets.
- Preserve room-owned item presentation overrides needed by ROOM2/3: rooms may change display/action text or inspection/read/combine presentation for their own context without rewriting baseline labels globally.
- Preserve room-owned combination policy outcomes needed by ROOM2/3: a room may accept/refuse/not-handle specific combinations, but inventory must not decide puzzle truth that belongs to that room. After an accepted combination replaces its inputs, the obsolete item card/detail closes while the inventory window itself remains available.
- The built-in ROOM1 newspaper combination is an explicitly preserved baseline exception. Do not add new room puzzle combinations as built-in shared-inventory truth.
- Inventory may expose ownership operations needed by current ROOM2/3 (`add once`, `try remove`, `replace pair`) only as shared ownership mechanics; progression, correctness, and rollback decisions remain in room Rules/Controllers.
- Do not invent item consumption, puzzle advancement, navigation, or story progression behavior in inventory.

## Failure/refusal behavior

- Full inventory refuses an add with no partial ownership change.
- Missing item refuses preview/open/remove requests without creating or consuming items.
- Unsupported combinations do not consume either item.
- Missing whole-newspaper sprite refuses the baseline newspaper combination without ownership mutation.
- Presentation or combination extension failures must fail closed: preserve existing ownership and transient UI state unless the room controller explicitly owns and accepts a state transition. A successful combination must not leave a card/detail window bound to an input item that no longer exists.
- If a room-specific policy owner is gone or invalid, its override/policy must not continue to affect other rooms.

## Acceptance scenarios

- Starting ROOM1 creates exactly one persistent inventory manager; opening and closing via public calls and UI controls works, and the draggable header/close button exist.
- ROOM1/5 can collect their items, observe `HasItem`, preview non-phone items, and open the phone through `ItemActivated` without changing their puzzle logic.
- Combining `TornNewspaper` and `TornPiece` yields one `WholeNewspaper`, raises one combination event, and does not consume the completed newspaper when previewed/read.
- Pointer presses on the inventory button/window/header/preview do not trigger room hotspots in the same gesture.
- ROOM2/3 can drag an owned item out of inventory, have the room resolve the release, and keep ordinary room clicks blocked during that gesture.
- ROOM2/3 can keep their current rule-owned outcomes for USB/computer, hook/sink, sink photos/enlarger, old photo combination, letters/board, combined photo/album, tissue/mirror, and notice reading without moving those truths into inventory.
- A successful room-owned old-photo combination replaces the two input items, raises one completion event, clears selection, and closes the stale input card/detail without closing the inventory window.

## Evidence / oracles

- Primary baseline: `git show 06bfbc1:Assets/Scripts/InventoryManager.cs` at blob `2e43c64288ae69aa8a9e08c75ca09538450953df`.
- Snapshot consumers/tests: `git show 06bfbc1:Assets/Editor/Room1BatchSmokeTest.cs` and `git show 06bfbc1:Assets/Editor/Room5BatchSmokeTest.cs`.
- Compatibility evidence only: current ROOM2/3 controllers, views, drop flows, and focused inventory tests in `Assets/Scripts/Room2`, `Assets/Scripts/Room3`, and `Assets/Editor`.
- Runtime observation or focused tests may confirm behavior, but implementation success logs are not acceptance evidence.

## Deferred choices

UI redesign, concrete adapter shape, class extraction, item art polish, save/load inventory persistence, and cross-room progression semantics are out of scope for this Spec.
