# ROOM1 branch mechanism comparison

## Comparison snapshot

This document compares ROOM1-related code at two fixed points:

- **Upstream ROOM1 implementation:** `origin/inventory-implementation` at `2146051`
- **Local ROOM1 refactor:** `room2-minju` at `10829c1`
- **Common base:** `2e39382`

The comparison is about interaction mechanisms, state ownership, and extension seams. Passwords, clue values, copy, and other game-content choices are intentionally out of scope.

## The two concepts

### Upstream ROOM1 implementation

The upstream branch is a **playable feature implementation**. Its main concern is completing the current ROOM1 flow with concrete scene behavior:

- `Room1GameController` owns room progress and talks directly to `InventoryManager`.
- `Room1ImageNavigationUI` presents image-based viewpoints and puzzle input.
- Inventory interaction is slot-oriented: selecting and clicking items drives combination or `ItemUsed` events.
- A single drawer-open fact represents the desk interaction.
- Keypad behavior is implemented directly as a view-specific digit buffer, delete, submit, and unlock flow.

Its strength is that the intended ROOM1 sequence is assembled end to end. Its limitation is that item acquisition, inventory presentation, room-object state, and input arbitration remain closely coupled to ROOM1's concrete UI.

### Local ROOM1 refactor

The local branch is a **mechanism extraction and UX-state refactor**. Its main concern is making ROOM1 interactions explicit and reusable by later rooms:

- `IInventoryAccess` defines the minimum ownership and selection seam needed by room logic.
- `Room1FieldItemAcquisition` gives field acquisition a typed, idempotent result.
- Field objects distinguish **inspect**, **acquire**, and **cancel** instead of treating a hotspot click as automatic ownership.
- Left and right drawers are independent room objects with independent state.
- `InventoryItemPresentation` separates item identity and card action (`Inspect`, `Read`, `Use`) from slot mechanics.
- `InventoryManager` exposes explicit open/close, card, selection, and pointer-consumption behavior.
- A consumed inventory click is prevented from leaking into a room hotspot in the same frame.
- Tests cover ownership, duplicate prevention, selection, field-layer reconstruction, modal behavior, and cross-room inventory seams.

Its strength is a clearer state model and better extension surface. Its limitation is that substantial reusable behavior still lives inside the large ROOM1 view class rather than in shared components.

## Mechanism-by-mechanism comparison

| Concern | Upstream branch | Local branch | Assessment |
|---|---|---|---|
| Item acquisition | Hotspot/controller methods generally acquire immediately | Inspect/acquire/cancel flow with typed acquisition result | Local mechanism is safer and more expressive |
| Duplicate prevention | Call sites check ownership before `AddItem` | `AddItemOnce` plus `Room1FieldItemAcquisitionResult` | Local establishes a clearer idempotency contract |
| Inventory dependency | ROOM1 depends on concrete `InventoryManager` | Shared operations exposed through `IInventoryAccess` | Local seam is the better basis for ROOM2+ |
| Inventory interaction | Slot selection, combination, and repeated-click use | Card presentation with explicit Inspect/Read/Use actions | Local avoids accidental use and supports richer items |
| Selection state | Internal index used mainly by slot behavior | Explicit select/get/clear API | Local enables selected-item-to-target mechanics |
| Room objects | One drawer-open state | Independent left/right drawer state | Local domain model matches two independently operable objects |
| Field-object visibility | Mostly encoded in viewpoint/background behavior | Ownership and object state reconstruct independent layers/hotspots | Local makes persistence rules testable |
| Input arbitration | Inventory overlap check | Inventory claims pointer-down before room view; outside-close click remains consumed | Local fixes a real same-frame input leak |
| Puzzle input | Concrete keypad buffer and submit flow | Some concrete flow was changed while refactoring | Preserve the generic input mechanism regardless of content values |
| Test strategy | End-to-end ROOM1 smoke around current flow | Broader contract checks for seams, idempotency, modal state, and reconstruction | Local coverage is stronger, but should retain an end-to-end player-flow test |

## Canonical concepts going forward

Use these terms consistently when combining the branches:

- **Room-object state:** facts such as drawer open/closed or door locked/unlocked. These exist independently of the current image/viewpoint.
- **Field-object availability:** whether an object is still present and interactable in the room. This is derived from room-object state and inventory ownership.
- **Inventory ownership:** whether the player carries an item. It must not be inferred from a modal, current view, or sprite visibility.
- **Presentation state:** temporary UI facts such as an open card, inspect overlay, highlighted hotspot, or entered keypad digits.
- **Item use:** applying an already-owned item to an action or target. It is distinct from acquiring or inspecting the field object.
- **Progress fact:** persistent gameplay state produced by a successful interaction. Closing a view must not roll it back.

This separation is the main value of the local refactor.

## What should be carried forward

Prefer the local mechanisms for:

1. `IInventoryAccess` and one-time acquisition semantics.
2. Independent room-object state rather than one state per background image.
3. Inspect/acquire/cancel field interactions.
4. Explicit inventory card actions and selection APIs.
5. Pointer ownership that prevents one click from affecting inventory and room UI.
6. Tests that assert ownership, persistence, idempotency, and view reconstruction separately.

Preserve from upstream as mechanisms:

1. A complete player-flow smoke test through ROOM1.
2. Generic keypad behavior: bounded digit input, deletion/clear, submission, wrong-input feedback, and persistent unlock state.
3. Image-navigation behavior that allows puzzle surfaces to reopen without fabricating or losing progress.
4. The existing persistent inventory lifetime across scenes.

Content values can change without changing these contracts.

## Remaining design debt

### `Room1ImageNavigationUI` is still too broad

The local branch adds better concepts but implements many of them inside one large view class: hotspot registration, layered room objects, field-item modals, acquisition routing, inventory blocking, image navigation, and puzzle UI. ROOM2 or ROOM3 should not copy this class-level structure.

Likely shared extraction candidates are:

- field-item choice/inspect overlay;
- room-object layer visibility binding;
- selected-item-to-target input adapter;
- reusable keypad input component.

### The inventory seam is only partial

`IInventoryAccess` is useful for room rules and controller tests, but ROOM1 still needs concrete `InventoryManager` events and presentation methods. Treat the interface as a gameplay seam, not as a complete inventory abstraction.

### Selection lifetime needs an explicit UX policy

The local inventory can preserve selection after closing or cancelling a card. That supports selected-item-then-target use, but the UI must visibly communicate selection. Otherwise a later room target can receive an item the player no longer realizes is active.

### Mechanism and content edits are mixed in the same branch

The local branch changes interaction architecture and some ROOM1 flow/content at the same time. Integration should be responsibility-based rather than whole-file replacement, so content differences do not obscure mechanism review.

## Recommended integration approach

Do not choose one branch wholesale. Use the upstream branch as the current playable baseline and port the local mechanisms by responsibility:

1. Add the inventory access and typed acquisition seams.
2. Add explicit field-item ownership and inspect/acquire/cancel behavior.
3. Split independent room objects into independent state facts.
4. Port compact inventory cards and pointer-consumption handling.
5. Retain or rebuild generic keypad/input behavior independently of its content values.
6. Reconcile tests so both mechanism contracts and one complete ROOM1 player flow pass.
7. Only after ROOM1 is stable, let ROOM2 consume the shared seams rather than ROOM1 view internals.

The target model is therefore:

```text
upstream playable flow
+ local explicit state/ownership/input mechanisms
- branch-specific content assumptions
- copied ROOM1 view internals in later rooms
```
