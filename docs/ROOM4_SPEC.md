# ROOM4 Gameplay/Product Specification

**Status:** The historical puzzle draft below is not the current integrated implementation; see [ROOM4_CURRENT_SNAPSHOT.md](ROOM4_CURRENT_SNAPSHOT.md). The following presentation amendment is user-confirmed.

## Final interaction follow-up

- Seed a short three-point example on the first map visit only. Players can clear or extend it before film use; ON hides/protects the route and OFF makes it editable again. No reseeding after clear/revisit and no answer recognition.
- Successful door input waits for shared unlock feedback, then automatically opens the completion overlay. Only its explicit ROOM5 button loads the next scene; expiration still blocks completion.
- Outside the aspect-fitted paper region, a press dismisses NewspaperOne/Two or MemoOne/Two to RightWall. Outside CrosswordCloseup returns to NewspaperTwo first. Inside-paper clicks remain available for normal inspection. Inventory consumption/coverage has priority; the dismissal press does not activate the exposed view underneath.
- Empty drawers can close/reopen after film acquisition without respawn. Timer colors are unchanged in this ROOM4 pass.
- Smoke coverage is updated for outside-paper round trips, example-route clear/revisit, empty drawers and delayed completion. Tests have not yet been executed; the earlier film-drop failure remains an unresolved regression item, not evidence of a passing full-room run.

## Latest QA amendment — direct interactions and hidden guidance

Understood as: hide separate ROOM4 hover/result hints, auto-check four keypad digits without confirm/delete steps, replace the film-containing drawer art, acquire film without choices, and remove added map answer labels. Do not change timer colors in this pass.

- Hide guidance bars, not clue-bearing artwork or operational controls. Both drawer and door codes auto-submit on the fourth digit; wrong codes clear for retry. Preserve passwords, success feedback delay and progression prerequisites. Added drawer confirm/delete controls are absent; */# on the door art have no click action.
- Use `ArtSource/서랍안에 필름있음.png`, copied unchanged to `Assets/4번방/오른쪽벽 부근/서랍안에 필름있음_QA.png`, for the open, uncollected drawer. Library and builder point to this copy. Preserve source/old assets, remove the duplicate film object rendering, and keep the empty open-drawer image after collection.
- Film click acquires directly and once, subject to capacity. Map application still uses the inventory drop and consumes the film only on success. Do not overlay Z8/Y6 text; retain mapped film artwork, ON/OFF and route controls.
- These amendments override conflicting earlier presentation notes below. Smoke expectations cover direct film acquisition and hidden labels; execution remains pending. The earlier ROOM4 film-drop smoke failure is not claimed fixed by this UI pass.

## Confirmed presentation amendment — crossword inspection

- Keep the existing right-wall and memo-pad artwork and existing hint sentences for QA. Wall replacement is not a prerequisite for the next demonstration; evaluate whether the existing image/text causes confusion during play.
- From the second newspaper, clicking its upper crossword panel opens the supplied `ArtSource/낱말퀴즈.png` as a dedicated closeup. The runtime copy is `Assets/4번방/오른쪽벽 부근/낱말퀴즈_확대.png`.
- Back from the closeup returns to that newspaper; another Back returns to the right wall. The lower weather/lifestyle sections do not open the crossword closeup.
- This adds inspection only: no answer entry, collection, consumption or puzzle-rule changes. Existing hint policy remains unchanged pending QA.
- Acceptance: verify the clickable region, readable closeup, repeated opening and the two-step return path in actual play. Inspection must preserve inventory/time and puzzle state.

## Historical puzzle draft

## 1. Source and context binding

This draft is bound to:

- the confirmed ROOM4 requirements supplied for branch `room2-minju`;
- the cross-room ownership and shared-system context in that instruction; and
- the ROOM3 success boundary in [`ROOM3_SPEC.md`](ROOM3_SPEC.md): setting the calendar to `2015-05-20` opens the ROOM3 exit and transitions to ROOM4; and
- ROOM3's established five-person festival preparation committee group, which supplies the referent for ROOM4's “among the five” finding without identifying the player as 김민준.

[`ROOM2_SPEC.md`](ROOM2_SPEC.md) is continuity context only. This draft does not modify or reinterpret ROOM2 or ROOM3.

Another developer owns ROOM1 and is expected to provide shared inventory, timer, item acquisition/use, combination, transition, and possibly drag/drop or selection behavior. Exact APIs and some interaction semantics remain unresolved. This specification states player-observable ROOM4 outcomes without treating an anticipated shared API as fixed.

Throughout this specification:

- **Confirmed** means required by the supplied ROOM4 intent.
- **Provisional v0 content** means a replaceable value or text choice used to make the first playable version determinate; it is not an immutable narrative fact.
- **Integration assumption** means behavior needed for continuity but not yet guaranteed by a concrete shared-system contract.
- **TBD** marks a genuine unresolved content or integration choice that implementation must not silently invent.

## 2. Purpose, success terminal, and non-goals

### Purpose

Define the bounded ROOM4 investigation: discover that four victims were deliberately tracked; solve two evidence-drawer clue branches; inspect documents connecting the group to a 2015 school festival preparation-room fire and documenting 김민준's punishment; complete a four-entry numbered crossword; and use its digits to leave for ROOM5.

### Player-visible success terminal

ROOM4 succeeds when the player enters the four-digit code derived from the numbered crossword at the final exit, the exit unlocks, and the player can transition to ROOM5. Under provisional v0 content, that code is `5173`.

### Explicit non-goals

This specification does not:

- implement code or prescribe Unity assets, scenes, scripts, prefabs, hierarchy, classes, APIs, or serialization;
- edit or redesign ROOM1, ROOM2, ROOM3, or their shared systems;
- identify the player as 김민준;
- identify the person who tracked the victims or any tracker/attacker;
- explain why the player's tracking dossier is absent;
- invent victim identities, fire causes beyond the documented arson charge, exact dates not supplied, an exact prison sentence, or unprovided article details;
- resolve `퇴학` versus `학교 이탈` before the content choice is confirmed;
- turn photo ordering into an additional deduction puzzle;
- impose a needless total order on independent investigation branches;
- require final art, embedded-number artwork, animations, audio, or styling for v0;
- choose concrete drag/drop, selection, text-entry, combination, item-consumption, save/load, timer, or transition implementation where the contract is silent.

## 3. Preconditions and cross-room assumptions

### Confirmed preconditions

- The player enters ROOM4 through ROOM3's open exit after ROOM3's calendar is set to `2015-05-20`.
- Prior story continuity has established a five-person festival preparation committee group. ROOM4 connects its evidence to that group but does not newly assign the player's or victims' identities.

### Integration assumptions and TBDs

- **Integration assumption:** the ROOM3-to-ROOM4 transition occurs once per completion event and does not duplicate or roll back shared progress.
- **Integration assumption:** ROOM4 can use the eventual shared inventory, acquisition/use, combination, inspection, and transition behavior for the transparent film and applicable room targets.
- **Integration assumption:** if a shared timer exists, ROOM4 integration does not accidentally reset, duplicate, pause, or stop it. No ROOM4-specific timer rule is confirmed.
- **TBD:** the exact shared-system API and progress lifetime across transitions or save/load boundaries.
- **TBD:** whether film/map use and photo placement use drag/drop, selected-item use, click-to-slot, or another owner-approved interaction. The observable results below do not require multiple interaction modes.

No carried item from ROOM1–ROOM3 is a confirmed prerequisite for a ROOM4 puzzle.

## 4. Observable dependency flow

ROOM4 must preserve the following dependencies without forcing independent branches into one total order:

```text
Tracking-wall inspection ───────────────────────────────→ story finding: four victims were deliberately tracked
          └─────────────────────────────────────────────→ anomaly: no corresponding player dossier is present

Collect transparent film → correctly use it on route map → first two digits (provisional: 27) ┐
                                                                                               ├→ concatenate in fixed order → evidence code (provisional: 2746)
Place photos 1–4 in matching slots 1–4 → second two digits (provisional: 46) ─────────────────┘
                                                                                                      ↓
                                                                                         unlock evidence drawer
                                                                                                      ↓
                                                          inspect fire article + discipline record + court article
                                                                                                      ↓
                                                   complete numbered crossword entries 1→2→3→4
                                                                                                      ↓
                                                               read one hidden digit per correct answer
                                                                                                      ↓
                                                            final code (provisional: 5173) → ROOM5 exit
```

The map/film branch and four-photo branch may be completed in either order. Tracking records and already accessible room objects may be inspected before, between, or after those branches. The evidence documents and crossword require access to the locked evidence drawer/cabinet. The final exit may be inspected or attempted early, but only the correct code unlocks it.

The logic contract does not require the player to have opened every clue before a correctly entered code is accepted unless a later approved product requirement explicitly adds such a gate.

## 5. Object, item, and clue catalog

| Object/item/clue | Initial/source state | Required role and repeat behavior | Acquisition / consumption status |
|---|---|---|---|
| Four victim photos | Displayed on the left wall and labeled `1`–`4` | Each contains part of one digital-number composite; remains inspectable before and after use | Room clues; whether temporarily selected/moved or represented elsewhere is Design |
| Four organized tracking records | Associated with the victim wall | Show deliberate records of routes, schedules, habits, and meetings; remain reinspectable | Retained room clues |
| Missing player dossier position/absence | Observable on or in relation to the tracking display | Supports only the observation that no corresponding player tracking dossier is present; remains observable | Absence/anomaly, not an item or explained fact |
| Transparent film | One collectible in a drawer | Alone shows incomplete digit marks and suggests use as an overlay; cannot duplicate; remains inspectable as required to understand its purpose | One-time collectible; retained, consumed, or transformed after correct use is **TBD** |
| Victim-route map | Room clue/target | Correct film use reveals the first two-digit clue; revealed result remains reinspectable | Room object; no acquisition required |
| Photo composite view with slots `1`–`4` | Available puzzle surface | Accepts each correspondingly labeled photo; all four correct placements reveal the second two-digit clue | Placement representation and post-placement photo state are **TBD**; correction/retry is required |
| Separate evidence drawer/cabinet | Initially locked | Accepts the concatenated four-digit evidence code; remains unlocked after success | Room object |
| Fire article | Inside evidence storage | Establishes the bounded fire facts; remains reinspectable | Retained document clue; inventory status **TBD** |
| School disciplinary record | Inside evidence storage | Documents 김민준's major school discipline and later expulsion or departure wording, subject to the unresolved content choice | Retained document clue; inventory status **TBD** |
| Court article | Inside evidence storage | Documents imprisonment of 김민준 for arson charges; remains reinspectable | Retained document clue; inventory status **TBD** |
| Numbered crossword | Below or with the court article | Most entries are prefilled; exactly four numbered entries are blank; correct answers reveal one hidden digit each | Retained puzzle/clue surface |
| Final exit input and exit | Initially locked | Accepts the four crossword digits in numbered order; wrong codes permit retry; correct code unlocks ROOM5 transition | Persistent room progress |

All revealed clues and documents must remain available for reinspection. Repeat interactions must not duplicate one-time collectibles or roll progress backward.

## 6. Fixed logic and provisional content boundary

### Fixed gameplay/story logic

The following are fixed by this draft's confirmed intent:

- four victim records exist and no corresponding player dossier is present;
- one transparent film must be correctly used with the victim-route map to reveal a two-digit clue;
- the four labeled victim photos must be placed into correspondingly numbered fixed slots to reveal another two-digit clue;
- the evidence code concatenates `[map + film two digits][victim-photo two digits]` in that exact order;
- the evidence drawer exposes the specified fire, school-discipline, and court findings;
- exactly four numbered crossword entries are blank, each correct answer exposes one hidden digit, and digits are read in explicit problem-number order `1 → 2 → 3 → 4`;
- the resulting four-digit crossword code unlocks the final exit and ROOM5 transition;
- wrong codes never unlock and never erase progress.

### Replaceable provisional v0 content

The following values and text make v0 playable but may be replaced later without changing the room flow or logic contract:

| Content | Provisional v0 value |
|---|---|
| Map + film clue | `27` |
| Victim-photo composite clue | `46` |
| Evidence-drawer code | `2746` |
| Crossword entry 1 | clue `축제 준비위원회가 사용하던 공간`; answer `준비실`; hidden digit `5` |
| Crossword entry 2 | clue `2015년에 발생한 사건`; answer `방화`; hidden digit `1` |
| Crossword entry 3 | clue `투명 필름과 겹쳐 본 것`; answer `지도`; hidden digit `7` |
| Crossword entry 4 | clue `징계 및 형사처벌을 받은 학생`; answer `김민준`; hidden digit `3` |
| Final exit code | `5173` |

These numbers, clue phrasings, answers, and hidden digits are product content, not fixed narrative truths or prescribed constants/configuration architecture. Later content revision may replace them as one internally consistent set while preserving the two-plus-two evidence-code structure and four-answers/one-digit-each/numbered-order exit structure.

## 7. Puzzle and story contracts

### 7.1 Tracking wall

- The left wall presents four victim photos and organized tracking records for each victim's routes, schedules, habits, and meetings.
- The organization and detail must support the player-visible conclusion that the four victims were deliberately tracked, rather than merely appearing in an incidental photo collection.
- There is no corresponding player dossier or player tracking record.
- The missing dossier is presented as an observed anomaly only. ROOM4 must not explain the absence, infer that the player created the records, or identify a tracker/attacker.
- The tracking records and photos remain reinspectable.

### 7.2 Transparent film and route map

- A drawer contains exactly one collectible transparent film.
- Viewed by itself, the film does not form complete digits. Its markings must perceptibly suggest that it is intended to overlay another drawing or map.
- Correctly using/aligning it with the victim-route map reveals one legible two-digit clue.
- Under provisional v0 content, that clue is `27`.
- Film use while the film is unavailable, or use against a wrong target, does not reveal digits, consume an unrelated item, or record success.
- Once correctly revealed, the map/film clue remains reinspectable. Repeating correct use does not award another film or duplicate progress.
- Exact alignment interaction and tolerance are Design choices, provided accidental or clearly incorrect use does not count as success.

### 7.3 Four-photo digital-stroke composite

- Each victim photo contains one fragment of digital-number strokes.
- Photos are visibly labeled `1`, `2`, `3`, and `4`.
- The composite view provides fixed, corresponding slots `1`, `2`, `3`, and `4`.
- A photo counts as correctly placed only in its matching numbered slot.
- Missing photos, duplicate use, or incorrect placement does not reveal the completed two-digit clue. The player can correct and retry without losing a photo or unrelated progress.
- Correct placement of all four photos reveals one legible two-digit clue. Under provisional v0 content, that clue is `46`.
- Slot labels deliberately remove sequence ambiguity: v0 does not require the player to deduce photo order.
- The completed composite and its digits remain reinspectable.

### 7.4 Evidence-drawer code

- The first and second clues are concatenated in the fixed order `[map + film][victim-photo composite]`.
- Under provisional v0 content, `27` followed by `46` produces `2746`.
- The locked evidence drawer/cabinet is separate from the drawer containing the film.
- Submitting the correct four-digit evidence code unlocks the evidence storage.
- Any other submitted code leaves it locked, preserves all room progress, and permits retry.
- Once unlocked, it remains unlocked and does not duplicate its documents on repeat interaction.

### 7.5 Documents and bounded story findings

The unlocked evidence storage exposes all three core documents:

1. **Fire article:** in 2015, a fire occurred in the festival preparation room at `○○ High School`; the room was used by festival preparation committee students; several students were injured.
2. **School disciplinary record:** it communicates `학생명: 김민준 / 교내 방화 사건 관련 중징계 처분 / 이후 퇴학 또는 학교 이탈`.
3. **Court article:** 김민준 was sentenced to imprisonment for arson charges.

The final school-record wording must choose `퇴학` or `학교 이탈` only after that content choice is confirmed. Exact sentence length, exact dates, and article details not supplied here remain TBD and must not be invented.

Together with the tracking wall, the documents support these and only these required player-visible outcomes:

- four victims were deliberately tracked;
- the player's own tracking data is missing;
- the 2015 fire involved the festival preparation committee space and injured multiple students; and
- among the five-person group, 김민준 alone is documented as receiving major school discipline and imprisonment for arson.

The documents do not establish that the player is 김민준, identify who tracked or attacked anyone, explain the absent player dossier, or establish unprovided incident details. All documents remain reinspectable after first opening.

### 7.6 Numbered crossword

- The crossword appears below or with the court article.
- Most answers are prefilled. Exactly four entries are blank and explicitly numbered `1`, `2`, `3`, and `4`.
- Each blank entry has one clue and accepts its corresponding answer under the chosen input interaction.
- An incomplete or incorrect answer does not falsely reveal that entry's hidden digit; correction and retry remain possible without erasing already correct entries.
- Completing a correct answer visually reveals exactly one digit embedded in that answer's lettering. The hidden digit is a visual property of the completed answer, not a fifth answer or an inferred ordering rule.
- The player reads the four revealed digits in explicit problem-number order `1 → 2 → 3 → 4`. Persistent numbering must prevent spatial layout or completion order from creating a competing read order.
- Under provisional v0 content, the entries reveal `5`, `1`, `7`, `3`, producing `5173`.
- Correct entries and revealed digits remain reinspectable. Repeat entry or inspection does not duplicate progress.

Exact letter artwork, accepted text-entry details, normalization, input feedback, and whether answers are typed, assembled, or selected are Design/content choices. They must preserve the four-answer, one-digit-per-answer, numbered-order contract.

### 7.7 Final exit

- The final exit accepts a four-digit code.
- Under provisional v0 content, submitting `5173` unlocks it.
- Any other submitted code leaves it locked, preserves all acquired/revealed/completed progress, and permits retry.
- Once unlocked, the exit remains unlocked and permits ROOM5 transition/ROOM4 completion.
- Repeat interaction with an already unlocked or completed exit does not relock it or require duplicate completion effects.
- No additional clue-completion gate is required: the correct code is sufficient even if the player reached it without opening every optional inspection surface.

## 8. Required domain progress facts

ROOM4 must preserve the following gameplay facts for the required play/session continuity. These are domain facts, not prescribed classes, APIs, configuration files, or serialization structures:

- availability/acquisition of the one transparent film;
- whether the map/film result has been correctly revealed and remains inspectable;
- placement status of each victim photo in slots `1`–`4`;
- whether the photo composite is complete and its two-digit clue remains inspectable;
- whether the tracking records and absent-player-dossier anomaly are available for inspection;
- whether the evidence drawer/cabinet is unlocked;
- availability of each core document for reinspection;
- correctness/completion status of crossword entries `1`–`4` and availability of each hidden digit;
- whether the final exit is unlocked;
- whether ROOM4 completion/ROOM5 transition has occurred.

Repeat interactions must not roll these facts backward or duplicate one-time acquisitions, clues, documents, unlocks, or transitions.

## 9. Failure and repeat-interaction contracts

Feedback wording and audiovisual presentation are Design concerns, but refusal must be observable rather than a silent false success.

| Situation | Required observable outcome |
|---|---|
| Film drawer revisited after collection | No second film is granted |
| Film inspected alone | Incomplete marks are visible; complete clue digits are not falsely presented |
| Film use attempted while absent or against a wrong target | No clue is revealed, no unrelated item is consumed, and retry remains possible |
| Correct map/film result revisited | The same two-digit clue remains inspectable without duplicate progress |
| Photo composite attempted with fewer than four correct placements | Completed digits are not revealed; placed/carrying photos are not lost |
| Photo put in a nonmatching slot or duplicated | Placement does not count as correct; completion is refused; correction remains possible |
| Completed photo composite revisited | All four placements and the revealed digits remain inspectable |
| Wrong evidence code submitted | Evidence storage remains locked; all prior progress remains intact; retry remains possible |
| Evidence storage revisited after unlock | It remains unlocked; documents are not duplicated |
| Document closed and revisited | Its confirmed content remains readable without added or changed claims |
| Crossword answer is incomplete or wrong | That entry's hidden digit is not falsely revealed; other correct entries persist; correction/retry remains possible |
| Crossword entries completed in a different temporal or spatial order | Final digit order remains unambiguously `1 → 2 → 3 → 4` |
| Correct crossword entry or completed crossword revisited | Correct answer artwork and its hidden digit remain inspectable without duplicate progress |
| Wrong final code submitted | Exit remains locked; all room progress remains intact; retry remains possible |
| Correct final code submitted after wrong attempts | Exit unlocks and permits ROOM5 transition |
| Unlocked/completed exit revisited | Exit does not relock or require duplicate completion/transition effects |

## 10. Acceptance scenarios and evidence/oracles

The oracle is observed room state, exact visible content, retained item/progress identity, and actual lock/transition behavior—not an implementation's self-reported success message alone.

### A. Happy path to ROOM5

**Given** the player enters ROOM4 from ROOM3
**When** the player inspects the tracking wall, collects the film, correctly uses it on the map, places photos `1`–`4` in slots `1`–`4`, submits the concatenated evidence code, inspects the three documents, correctly completes crossword entries `1`–`4`, and submits the resulting final code
**Then** the tracking findings are available, both two-digit clues are revealed, evidence storage unlocks, all required documents are readable, each crossword answer exposes one hidden digit, the final exit unlocks, and the player can transition to ROOM5.

**Evidence/oracle:** direct captures/assertions of the wall organization and dossier absence; revealed map and photo-composite digits; actual evidence lock state; visible document text; numbered crossword answers/digits; and actual ROOM5 transition through the unlocked exit.

### B. Provisional-code verification

**Given** provisional v0 content is active
**When** the map/film result is read as `27` and the completed photo result as `46`
**Then** their fixed concatenation is `2746`, which unlocks evidence storage, while reversed or otherwise different values do not.
**And when** entries `1`–`4` are correctly completed with `준비실`, `방화`, `지도`, `김민준`
**Then** they visually reveal `5`, `1`, `7`, `3` in numbered order, and `5173` unlocks the final exit.

**Evidence/oracle:** independently read visible digits and answer artwork, concatenate by the displayed contract, then observe actual locked/unlocked boundaries for both correct and deliberately wrong/reversed codes. Configured answers alone are insufficient.

### C. Branch order flexibility and refusal boundaries

**Given** neither two-digit branch is complete
**When** the player completes the photo branch before collecting/using the film, or completes the film/map branch first
**Then** each branch retains its result and the other remains independently completable.
**When** the player attempts wrong-target film use, incomplete/wrong photo placement, a wrong evidence code, an incomplete/wrong crossword answer, or a wrong final code
**Then** no corresponding success fact is awarded, unrelated progress is preserved, and correction/retry remains possible.

**Evidence/oracle:** before/after domain-state and inventory identity at each refusal, followed by successful completion using the valid prerequisite/input in both branch orders.

### D. Reinspection and one-time stability

**Given** the film, map clue, photo clue, tracking records, documents, crossword digits, or unlocks have been obtained
**When** the player revisits their source and inspection surfaces after intervening interactions
**Then** the film is not duplicated, completed progress does not revert, and every revealed clue/document remains available with unchanged required content.
**And when** the already unlocked/completed exit is used again
**Then** it does not relock or require a second completion effect.

**Evidence/oracle:** item counts/identity and progress facts before and after repeated interactions, repeated visible-content comparisons, persistent lock states, and observation that repeat exit use does not create a second completion requirement.

### E. Absent-player-dossier observation

**Given** the tracking wall is inspected
**When** the player compares its four organized victim records with the available player-related tracking material
**Then** four victim dossiers are observable and no corresponding player dossier is present, while no explanation or tracker identity is asserted.

**Evidence/oracle:** complete interaction/visual sweep of the tracking display and associated records, plus review of player-visible ROOM4 text for the bounded anomaly. A hidden implementation flag alone is insufficient.

### F. Story claim fidelity and no overclaim

**Given** prior continuity has established the five-person festival preparation committee group and evidence storage is unlocked
**When** the player inspects all three documents and the tracking wall
**Then** the player can establish the four required story outcomes: deliberate tracking of four victims; absent player tracking data; a 2015 fire in the committee-used festival preparation room that injured several students; and documentation that 김민준 alone among the five received major school discipline and imprisonment for arson.
**And** the content does not identify the player as 김민준, identify a tracker/attacker, explain the missing dossier, choose unapproved `퇴학`/`학교 이탈` wording, or invent sentence length, exact dates, or article details.

**Evidence/oracle:** human-reviewed capture/transcript of every player-visible ROOM4 story surface compared against the bounded claims and prohibited overclaims, including both presence and absence checks.

### G. Read-order ambiguity prevention

**Given** crossword entries may be completed in any order and may occupy any visual arrangement
**When** all four correct answers reveal their digits
**Then** persistent labels `1`–`4` make `1 → 2 → 3 → 4` the sole instructed read order; completion order and spatial scanning do not imply alternatives.

**Evidence/oracle:** direct UI/content inspection under at least two completion orders, verifying stable numbering and the same resulting code.

### H. ROOM1–ROOM3 and shared-system non-regression

**Given** ROOM4 is integrated with shared inventory, timer, item use/combination, progress, and transitions
**When** existing ROOM1–ROOM3 acceptance/smoke coverage and an end-to-end ROOM3→ROOM4→ROOM5 continuity check run
**Then** prior-room puzzle and transition behavior remains passing; ROOM3 still transitions after `2015-05-20`; shared item interactions follow the owner-approved contract; the timer follows its owner-approved continuity rule; ROOM4 one-time state does not duplicate or roll back; and ROOM5 transition occurs only after the correct ROOM4 exit code.

**Evidence/oracle:** existing prior-room tests and observed room boundaries, supplemented by owner-approved shared-system checks. ROOM4-only success logs are insufficient evidence of non-regression.

## 11. Open integration and content questions

1. What is the final shared contract for item identity, acquisition, inspection, selection/use, combination/overlay, one-time collection, and duplication prevention?
2. Which single interaction model will ROOM4 use for film/map overlay and photo-to-slot placement: drag/drop, selected-item use, click-to-slot, or another owner-approved shared behavior?
3. After correct map use and photo placement, are the film and photos retained, consumed, or represented as transformed room state while preserving all required reinspection?
4. What is the owner-approved persistence lifetime for ROOM4 progress and the ROOM4-to-ROOM5 transition?
5. If a shared timer is present, what is its owner-approved behavior across ROOM3→ROOM4→ROOM5?
6. For the disciplinary record, which final wording is approved: `퇴학` or `학교 이탈`?
7. What final content set will replace or approve the provisional map digits, photo digits, crossword clues/answers/hidden digits, and both codes?
8. What exact sentence length, dates, and additional article wording, if any, will be supplied by narrative/content owners? Until supplied, they remain omitted rather than invented.

## 12. Design fence

Later Unity Design/content work owns:

- Unity scene layout, hierarchy, GameObjects, components, prefabs, assets, and object placement beyond the confirmed left-wall and drawer relationships;
- class/module names, APIs, data structures, content storage, serialization, save/load, and state ownership;
- shared inventory, timer, acquisition/use, combination/overlay, and transition integration;
- drag/drop, selection, alignment tolerance, slot placement, text entry, input normalization, and device/input handling;
- visual construction of photos, maps, records, documents, digital strokes, embedded crossword digits, and placeholder-to-final asset replacement;
- animations, audio, camera behavior, lighting, styling, feedback presentation, and accessibility treatment;
- exact document layout and any content still marked TBD;
- test harnesses, automation structure, fixtures, and implementation-level verification.

Placeholder visuals and text are acceptable for v0 if they preserve the observable contracts and clearly communicate required clues. Design and implementation must preserve fixed logic, replaceable-content boundaries, story limits, dependency flexibility, failure/repeat behavior, and unresolved choices without treating this Draft as user approval.
