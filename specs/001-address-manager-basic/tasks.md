# Tasks: C64 Address Manager

**Input**: Design documents from `specs/001-address-manager-basic/`\
**Prerequisites**: `plan.md` (required), `spec.md` (required), `research.md`,
`data-model.md`, `contracts/runtime-interface.md`, `quickstart.md`

**Validation**: Manual validation tasks are included for each user story and
mapped to `specs/001-address-manager-basic/quickstart.md` plus
`specs/001-address-manager-basic/validation-log.md`.

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Prepare implementation scaffolding and traceability for a
single-file C64 BASIC delivery.

- [ ] T001 Add an implementation section with BASIC line-range allocation in
      `specs/001-address-manager-basic/plan.md`
- [ ] T002 Add FR-to-routine traceability table (FR-001..FR-014) in
      `specs/001-address-manager-basic/plan.md`
- [ ] T003 [P] Add task-oriented test data set (at least 10 sample contacts
      incl. duplicate last names) in
      `specs/001-address-manager-basic/quickstart.md`
- [ ] T004 [P] Create validation evidence template (story checkpoints,
      pass/fail, notes) in `specs/001-address-manager-basic/validation-log.md`
- [ ] T005 Document build + run workflow for VS64 and VICE in `README.md`
- [x] T044 Normalize duplicated/corrupted requirement wording and keep FR text
      stable in `specs/001-address-manager-basic/spec.md` before implementation
      _(done: FR-001..FR-014 restored, Refinement clarifications integrated)_

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Implement shared runtime foundations required by all user stories.

**CRITICAL**: No user story work begins before this phase is complete.

- [ ] T006 Define global constants for field limits, page sizes, and file names
      in `src/main.bas`
- [ ] T007 Implement unified screen clear/header/footer helpers for 40x25 output
      in `src/main.bas`
- [ ] T008 Implement central keyboard input dispatcher for menu/state navigation
      in `src/main.bas`
- [ ] T009 Implement pipe-safe field sanitizer and length validator routines in
      `src/main.bas`
- [ ] T010 Implement record encode routine for
      `NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON` in `src/main.bas`
- [ ] T011 Implement record decode/parsing routines using `INSTR`/`MID$` in
      `src/main.bas`
- [ ] T012 Implement disk read-all routine for `ADDRESSES.DAT` with sequential
      I/O in `src/main.bas`
- [ ] T013 Implement disk write-safe routine (temp buffer + commit/replace) in
      `src/main.bas`
- [ ] T014 Implement global `ONERR` error handler with user-facing messages and
      safe return path in `src/main.bas`
- [ ] T015 Implement state machine skeleton for 7 screen states in
      `src/main.bas`
- [ ] T016 Implement shared pagination helper for exactly 8 items per page in
      `src/main.bas`
- [ ] T017 Add startup/shutdown flow (main menu entry + clean program exit) in
      `src/main.bas`

**Checkpoint**: Shared runtime is stable; user-story flows can be added without
reopening core I/O and state foundations.

---

## Phase 3: User Story 1 - Neue Adresse erfassen (Priority: P1) 🎯 MVP

**Goal**: Create and persist new address entries from a dedicated input form.

**Independent Validation**: Start program, open create flow, input all 6 fields,
save, restart program, and confirm record still exists.

### Validation Tasks (Required)

- [ ] T018 [P] [US1] Add explicit US1 validation checklist and expected outputs
      (including timed checks for SC-001 and SC-002) in
      `specs/001-address-manager-basic/quickstart.md`
- [ ] T019 [US1] Execute US1 manual validation and record evidence in
      `specs/001-address-manager-basic/validation-log.md` (menu recognition <=30
      sec, entry capture <=2 min)

### Implementation Tasks

- [ ] T020 [US1] Implement "new entry" route: Eintrag verwalten → Taste N →
      Erfassungsmaske in `src/main.bas`
- [ ] T021 [US1] Implement 6-field input form with per-field max length
      enforcement in `src/main.bas`
- [ ] T022 [US1] Implement required-field checks: reject save if Nachname or
      Vorname empty; show retry prompt; Email/Strasse/Ort/Telefon may be blank
      in `src/main.bas`
- [ ] T023 [US1] Implement save action for new entry using foundational write
      routine in `src/main.bas`
- [ ] T024 [US1] Implement cancel/abort path that discards unsaved input in
      `src/main.bas`
- [ ] T025 [US1] Implement success message transition to main menu; on I/O error
      show message then return to previous screen in `src/main.bas`

**Checkpoint**: User Story 1 is independently usable and testable as MVP.

---

## Phase 4: User Story 2 - Vorhandene Adresse suchen und verwalten (Priority: P2)

**Goal**: Search by exact last name, select current record, then
edit/copy/delete with confirmation.

**Independent Validation**: With duplicate last names stored, search exact
match, select by sequence number, edit one record, copy one record, delete one
record with confirmation, and verify persistence.

### Validation Tasks (Required)

- [ ] T026 [P] [US2] Add explicit US2 validation path (including duplicate
      last-name cases) in `specs/001-address-manager-basic/quickstart.md`
- [ ] T027 [US2] Execute US2 manual validation and record evidence in
      `specs/001-address-manager-basic/validation-log.md`

### Implementation Tasks

- [ ] T028 [US2] Implement exact last-name search input flow in `src/main.bas`
- [ ] T029 [US2] Implement result list rendering with sequential numbering
      (1..N) in `src/main.bas`
- [ ] T030 [US2] Implement result selection by number and current-record buffer
      assignment in `src/main.bas`
- [ ] T031 [US2] Implement edit action prefilled from current record and save
      changes in `src/main.bas`
- [ ] T032 [US2] Implement copy action that clones selected record to a new
      entry in `src/main.bas`
- [ ] T033 [US2] Implement delete confirmation screen (`J/N`) and confirmed
      delete flow in `src/main.bas`
- [ ] T034 [US2] Implement delete abort path that preserves original record
      unchanged in `src/main.bas`
- [ ] T035 [US2] Implement no-match message flow with return to search/menu in
      `src/main.bas`
- [ ] T036 [US2] Ensure write/read failure paths keep prior persisted data
      intact and return to the screen that triggered the I/O operation in
      `src/main.bas`

**Checkpoint**: User Story 2 is independently validatable and does not regress
US1.

---

## Phase 5: User Story 3 - Gesamtliste anzeigen (Priority: P3)

**Goal**: Display all stored addresses in readable paginated form.

**Independent Validation**: Create multiple entries, open full list, paginate
forward/backward, and confirm complete coverage in storage order.

### Validation Tasks (Required)

- [ ] T037 [P] [US3] Add explicit US3 full-list validation path and paging
      checks in `specs/001-address-manager-basic/quickstart.md`
- [ ] T038 [US3] Execute US3 manual validation and record evidence in
      `specs/001-address-manager-basic/validation-log.md`

### Implementation Tasks

- [ ] T039 [US3] Implement full-list screen route from main menu in
      `src/main.bas`
- [ ] T040 [US3] Implement paginated list rendering in storage order in
      `src/main.bas`
- [ ] T041 [US3] Implement next/previous page navigation and page indicator in
      `src/main.bas`
- [ ] T042 [US3] Implement empty-dataset message for full-list flow in
      `src/main.bas`
- [ ] T043 [US3] Implement return-to-main-menu behavior from all full-list
      screens in `src/main.bas`

**Checkpoint**: User Story 3 is independently validatable and integrated with
prior stories.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final hardening, documentation, and full workflow validation.

- [ ] T045 Improve inline BASIC comments for non-obvious
      parser/I/O/state-machine sections in `src/main.bas`
- [ ] T046 [P] Update user-facing controls and workflow notes in `README.md`
- [ ] T047 Re-run complete 9-phase validation and log final pass/fail in
      `specs/001-address-manager-basic/validation-log.md`
- [ ] T048 Verify performance/memory targets and record observed values in
      `specs/001-address-manager-basic/plan.md`
- [ ] T049 Run final VS64 build and confirm output artifact availability in
      `build/AddressManagerBasic.prg`

---

## Dependencies & Execution Order

### Phase Dependencies

- Phase 1 (Setup): start immediately
- Phase 2 (Foundational): depends on Phase 1 and blocks all user stories
- Phase 3 (US1): depends on Phase 2
- Phase 4 (US2): depends on Phase 2 (can start after Phase 2, but recommended
  after US1 MVP)
- Phase 5 (US3): depends on Phase 2 (can start after Phase 2, recommended after
  US2)
- Phase 6 (Polish): depends on completion of selected stories

### User Story Dependency Graph

- US1 (P1) -> MVP baseline
- US2 (P2) -> builds on shared foundations, should preserve US1 behavior
- US3 (P3) -> builds on shared foundations, should preserve US1 and US2 behavior

Recommended completion order: **US1 -> US2 -> US3**

---

## Parallel Opportunities

- Setup: `T003` and `T004` can run in parallel (different files)
- US1: `T018` can run in parallel with `T020`-`T022`
- US2: `T026` can run in parallel with `T028`-`T030`
- US3: `T037` can run in parallel with `T039`-`T040`
- Polish: `T046` can run in parallel with `T045`/`T048`

## Parallel Example: User Story 1

- Run `T018` while implementing `T020`-`T021`
- After code is stable, execute `T019` and then proceed with `T023`-`T025`
  adjustments

## Parallel Example: User Story 2

- Run `T026` while implementing `T028`-`T030`
- Implement `T031`/`T032` and `T033`/`T034` as separate sub-flows, then converge
  on `T036`

## Parallel Example: User Story 3

- Run `T037` while implementing `T039`-`T041`
- Use `T042` and `T043` as finishing tasks once list flow compiles

---

## Implementation Strategy

### MVP First (US1 only)

1. Complete Phase 1 and Phase 2
2. Complete Phase 3 (US1)
3. Validate with `T019`
4. Demo/deploy MVP behavior

### Incremental Delivery

1. Foundation complete
2. Deliver US1
3. Deliver US2
4. Deliver US3
5. Apply Phase 6 polish and full validation

### Single-File BASIC Practicality

- Keep `src/main.bas` organized in contiguous line ranges by feature block
- Commit after each completed task group (foundation, US1, US2, US3, polish)
- Rebuild frequently to catch regressions early
