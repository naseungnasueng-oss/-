# Shared System Integration Spec

## Current integration checkpoint: interaction QA and test distribution

The sections under **Historical phase boundary** below describe the earlier adoption phase, not today's integrated ROOM1–5 state. Current timer/transition behavior is recorded in [SHARED_CURRENT_SNAPSHOT.md](SHARED_CURRENT_SNAPSHOT.md); the confirmed Back-navigation amendment is in [SHARED_INVENTORY_SPEC.md](SHARED_INVENTORY_SPEC.md).

### ROOM1 QA override

The later confirmed [ROOM1 revision](ROOM1_QA_REVISION.md) overrides shared presentation where specified: ROOM1 hides hover/result guidance and hotspot emphasis, uses direct USB/photo pickup and field-only phone use, permits empty drawers to close, closes newspaper inspection on outside-paper click, and attempts completion automatically after successful unlock feedback. Shared inventory becomes 2×6 with first-open lower-right placement and subsequent position retention. ROOM2's subsequent [QA amendment](ROOM2_SPEC.md#current-qa-interaction-amendment) hides guidance, grants direct field pickups, opens the enlarger workspace without a use modal, and attempts completion after unlock feedback. ROOM3–5 keep their existing rules in this ROOM1–2 checkpoint.

### Shared hotspot appearance contract

- Field hotspot regions are invisible at rest. On hover, show a faint box-skin cue rather than a flat colored rectangle; existing hint sentences remain available.
- The visual reference is `C:/Users/Minju/testproject`, `Room1Prototype.DrawHotspot()`. Reusing its runtime box skin in uGUI is not a claim of pixel-identical IMGUI rendering.
- All five rooms share skin, tint and alpha through `Assets/Resources/HotspotVisualSettings.asset`. Existing visible buttons keep their own backgrounds; drag-target emphasis remains separately configurable.
- Keep room-specific position, size, eligibility and action code unchanged. Do not introduce room/screen-specific alpha overrides yet: perceived contrast differs with the background, but the user deferred that decision until QA.
- Acceptance: compare hover/rest states on light and dark backgrounds, check that hints and click targets still work, and ensure highlights neither obscure inventory/modals nor leave a solid-color flash. Current tuned values and verification limits are recorded in the shared snapshot.

### Next cycle and release boundary

1. Replace the pending ROOM4 full right-wall artwork, including the memo pad, then verify hotspot alignment and clue readability. No improvised wall replacement is requested.
2. Run an internal demonstration using an actual build, ROOM1 through ending. Inventory continuity, the shared timer, progression and the ending are essential checks.
3. Fix progression blockers and required clue/asset omissions before limited test distribution. Broader refactoring is deferred until QA identifies concrete recurring problems.
4. Background music, sound effects and presentation refinement are polishing work unless their absence blocks a required clue. Inventory incomplete assets/audio during the demonstration; do not mark them complete merely because the game compiles.
5. Plan limited test distribution after the demonstration and fixes; itch.io was discussed, but no page creation, upload, public release or push is authorized by this checkpoint. Packaging, controls/known-issues notes, version identification, feedback collection and asset-license checks remain preparation tasks.

This checkpoint records implementation and pending QA, not deployment readiness. Updated smoke tests have not been executed in Unity.

## Historical phase boundary

### Scope and authority

This document freezes the phase boundary for the current shared-system integration. It does not redesign any room puzzle. The authoritative baselines are:

- Inventory: ROOM1 snapshot at `06bfbc188c225a4990465856ebe553afdbf130fc`, `Assets/Scripts/InventoryManager.cs` blob `2e43c64288ae69aa8a9e08c75ca09538450953df`.
- Timer: shared timer snapshot at the same commit, including `RoomCountdownTimer.cs`, `RoomTimerSettings.cs`, and `Resources/RoomTimerSettings.asset`.

## Phase boundary

- Only Inventory and Timer are adopted shared gameplay systems in this phase.
- The inventory baseline is the ROOM1 behavior frozen in `docs/SHARED_INVENTORY_SPEC.md`.
- The timer baseline is the per-room shared timer behavior frozen in `docs/SHARED_TIMER_SPEC.md`.
- ROOM1, ROOM2, ROOM3, and ROOM5 puzzle logic is out of scope and must not be changed by this integration.
- ROOM2/3 may receive only the adapters/extensions needed to consume the shared inventory/timer contracts.
- Inventory may coordinate ownership, UI, item activation/preview, pointer ownership, and room-owned extension points. It must not become the source of puzzle truth.
- Timer may coordinate countdown, visible state, explicit deductions, stop, expiry overlay, inventory closure on expiry, and retry callback/default reload. It must not become the source of puzzle truth.

## Explicitly deferred choices

The following are later phases and must not be folded into this integration:

- ROOM4 integration.
- Final scene progression/navigation.
- Save/load.
- WebGL deployment.
- Performance optimization.
- Global wording/art polish.
- Broad room refactors or architecture rewrites.
- Cross-room timer persistence or global time budgets.
- Generalized scene progression. It may be named as a future shared-system candidate, but it is not specified now.

## Success terminal for this phase

The current success terminal is a common integration base that compiles and passes focused shared-system and room non-regression checks. This terminal means “safe base for further integration,” not final integration, deployment readiness, or content-complete gameplay.

Future changes to room logic require a separate confirmed Spec before mutation.

## Acceptance scenarios

- The shared inventory contract preserves ROOM1/5 observable inventory behavior while allowing ROOM2/3 to route item drag-release and room-owned presentation/combination outcomes through shared surfaces.
- The shared timer contract preserves ROOM1/5 12-minute per-room behavior while allowing ROOM2/3 to consume timer surfaces only where their room-owned rules confirm it.
- Focused non-regression checks show ROOM1/2/3/5 puzzle outcomes unchanged except for their consumption of the adopted shared inventory/timer contracts.
- ROOM2/3 adapters/extensions do not move correctness, progression, item-consumption policy, or rollback decisions out of room Rules/Controllers.
- No implementation claims final scene progression, ROOM4 readiness, save/load readiness, WebGL readiness, broad refactor completion, or deployment readiness as part of this phase.

## Stop condition

Stop this phase when:

1. `docs/SHARED_INVENTORY_SPEC.md`, `docs/SHARED_TIMER_SPEC.md`, and this document exist and freeze the contracts above;
2. a common integration base compiles; and
3. focused shared-system and room non-regression checks pass.

A blocking ambiguity must be reported and implementation must stop, but reporting it does not satisfy this phase's success terminal. If work requires changing any room puzzle logic, adding deferred systems, redesigning shared architecture beyond the contract surfaces, or claiming final deployment readiness, stop and request a separate confirmed Spec.

## Evidence / oracles

- Baseline source snapshots named above, read directly with `git show 06bfbc1:<path>`.
- Focused shared-system tests and room non-regression tests.
- Runtime observations of the relevant scenes where needed.
- Build/test pass logs may support verification, but they are not substitutes for source-snapshot and behavior oracles.

## Known ambiguity policy

When current ROOM2/3 behavior conflicts with the ROOM1 inventory baseline, preserve ROOM1 baseline behavior and report the fork. Do not silently reinterpret ROOM2/3 implementation details as shared baseline behavior.
