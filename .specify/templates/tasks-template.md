---

## description: "Task list template for feature implementation"

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/` **Prerequisites**:
plan.md (required), spec.md (required for user stories), research.md,
data-model.md, contracts/

**Validation**: Manual validation tasks are REQUIRED for every user story.
Automated test tasks are OPTIONAL and should only be included when explicitly
requested in the feature specification.

**Organization**: Tasks are grouped by user story to enable independent
implementation and validation of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Program code**: `src/main.bas` unless the plan explicitly introduces
  additional `.bas` files
- **Build output**: `build/`
- **Requirements and planning**: `req/`, `specs/`
- Paths shown below assume the existing single-program C64 BASIC structure

<!--
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.

  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Endpoints from contracts/

  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment

  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Capture the constraints that keep the feature implementable on a
Commodore 64

- [ ] T001 Record target screen states and key mappings in
      `specs/[###-feature-name]/spec.md`
- [ ] T002 Define record layout, field sizes, and memory budget in
      `specs/[###-feature-name]/plan.md`
- [ ] T003 [P] Confirm build and emulator workflow for the feature in
      `specs/[###-feature-name]/quickstart.md`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core routines that every story depends on and that protect memory,
screen, and disk behavior

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

Examples of foundational tasks (adjust based on your project):

- [ ] T004 Implement shared screen rendering and input helpers in `src/main.bas`
- [ ] T005 [P] Implement record parsing and formatting routines in
      `src/main.bas`
- [ ] T006 [P] Implement 1541 file open/read/write helpers in `src/main.bas`
- [ ] T007 Reserve variable and string usage to stay within the documented
      memory budget
- [ ] T008 Add recovery paths for invalid input and disk I/O failures in
      `src/main.bas`
- [ ] T009 Document any justified complexity exceptions in
      `specs/[###-feature-name]/plan.md`

**Checkpoint**: Foundation ready; user story work can begin without re-opening
memory or storage basics

---

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP

**Goal**: [Brief description of what this story delivers]

**Independent Validation**: [How to verify this story in an emulator or on
hardware]

### Validation for User Story 1 (REQUIRED) ⚠️

> **NOTE: Capture the manual validation flow before marking the story complete.
> Add automated tests only when the spec explicitly calls for them.**

- [ ] T010 [US1] Write the emulator or hardware validation steps in
      `specs/[###-feature-name]/quickstart.md`
- [ ] T011 [US1] Run and record the primary user journey with the required disk
      and input conditions

### Implementation for User Story 1

- [ ] T012 [P] [US1] Implement the screen flow for the story in `src/main.bas`
- [ ] T013 [P] [US1] Implement or extend the record layout logic in
      `src/main.bas`
- [ ] T014 [US1] Implement the story's disk access path in `src/main.bas`
      (depends on T013)
- [ ] T015 [US1] Add input validation and recovery messaging for the story
- [ ] T016 [US1] Verify memory use and string lengths against the plan budget
- [ ] T017 [US1] Update `req/Req.md` or feature docs if user-visible behavior
      changed

**Checkpoint**: At this point, User Story 1 should be fully functional and
independently validatable

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Validation**: [How to verify this story in an emulator or on
hardware]

### Validation for User Story 2 (REQUIRED) ⚠️

- [ ] T018 [US2] Extend `specs/[###-feature-name]/quickstart.md` with the
      story-specific manual validation path
- [ ] T019 [US2] Run and record the story-specific validation, including one
      failure scenario

### Implementation for User Story 2

- [ ] T020 [P] [US2] Implement the story's screen updates in `src/main.bas`
- [ ] T021 [US2] Implement the story's data handling in `src/main.bas`
- [ ] T022 [US2] Implement the story's disk or lookup behavior in `src/main.bas`
- [ ] T023 [US2] Integrate with User Story 1 flows without breaking their manual
      validation path

**Checkpoint**: At this point, User Stories 1 and 2 should both validate
independently

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Validation**: [How to verify this story in an emulator or on
hardware]

### Validation for User Story 3 (REQUIRED) ⚠️

- [ ] T024 [US3] Extend `specs/[###-feature-name]/quickstart.md` with the
      story-specific validation path
- [ ] T025 [US3] Run and record the story-specific validation, including
      recovery from bad input or disk state

### Implementation for User Story 3

- [ ] T026 [P] [US3] Implement the story's screen and input changes in
      `src/main.bas`
- [ ] T027 [US3] Implement the story's data mutation logic in `src/main.bas`
- [ ] T028 [US3] Implement the story's persistence or deletion flow in
      `src/main.bas`

**Checkpoint**: All user stories should now be independently validatable

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] TXXX [P] Documentation updates in `README.md`, `req/`, or feature specs
- [ ] TXXX Code cleanup and refactoring
- [ ] TXXX Performance optimization across screen redraws and disk access
- [ ] TXXX [P] Additional automated checks if explicitly requested in the spec
- [ ] TXXX Improve disk error messaging and recovery behavior
- [ ] TXXX Run quickstart.md validation

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user
  stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No
  dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - May integrate
  with US1 but should be independently validatable
- **User Story 3 (P3)**: Can start after Foundational (Phase 2) - May integrate
  with US1/US2 but should be independently validatable

### Within Each User Story

- Manual validation steps MUST exist before a story is considered complete
- If automated checks are included, they SHOULD fail before implementation work
  starts
- Screen flow and data format decisions before persistence logic
- Core implementation before integration
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if
  team capacity allows)
- Validation write-up and implementation can proceed in parallel once the flow
  is known
- Independent screen or data-handling tasks within a story marked [P] can run in
  parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Prepare User Story 1 validation alongside implementation:
Task: "Write the emulator or hardware validation steps in specs/[###-feature-name]/quickstart.md"
Task: "Implement the screen flow for the story in src/main.bas"

# Build independent story parts together:
Task: "Implement or extend the record layout logic in src/main.bas"
Task: "Implement the story's disk access path in src/main.bas"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Run the manual User Story 1 walkthrough independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Validate independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Validate independently → Deploy/Demo
4. Add User Story 3 → Validate independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = distinct work that can proceed without blocking dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and validatable
- Keep memory, screen, and disk constraints visible in task wording
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently
- Avoid: vague tasks, same file conflicts, cross-story dependencies that break
  independence
