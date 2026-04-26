<!--
Sync Impact Report
Version change: template -> 1.0.0
Modified principles:
- Principle 1 placeholder -> I. Memory Before Comfort
- Principle 2 placeholder -> II. One Screen, One Purpose
- Principle 3 placeholder -> III. Sequential BASIC Over Cleverness
- Principle 4 placeholder -> IV. 1541-Aware Persistence
- Principle 5 placeholder -> V. Emulator or Hardware Validation
Added sections:
- Platform Constraints
- Workflow & Review
Removed sections:
- None
Templates requiring updates:
- ✅ .specify/templates/constitution-template.md
- ✅ .specify/templates/plan-template.md
- ✅ .specify/templates/spec-template.md
- ✅ .specify/templates/tasks-template.md
- ✅ .github/prompts/*.md reviewed; no constitution-specific wording changes required
Follow-up TODOs:
- None
-->

# AddressManagerBasic Constitution

## Core Principles

### I. Memory Before Comfort

Every feature MUST start with an explicit memory budget for variables, arrays,
screen buffers, and record strings. A single BASIC string MUST never be assumed
to hold more than 255 characters, and any record layout that can exceed that
limit MUST define how it is split into logically related strings before coding
begins. Features that duplicate large datasets in memory, rely on hidden
buffers, or require speculative caching MUST include a written justification in
the implementation plan.

Rationale: On a Commodore 64, RAM and BASIC string handling are the first hard
limits. Memory oversights become functional defects, not polish issues.

### II. One Screen, One Purpose

Each interaction MUST fit within a deliberate 40x25 text screen state. Every
screen MUST have one primary purpose, visible key choices, and predictable
cursor flow without relying on hidden modes, overlapping windows, or dense
status clutter. Input and output flows SHOULD minimize full-screen redraws and
unnecessary re-entry of data.

Rationale: The user experience is constrained by a small text display and a
keyboard-only workflow. Clear screen ownership keeps the program usable.

### III. Sequential BASIC Over Cleverness

Implementations MUST prefer straightforward BASIC control flow, explicit data
formats, and locally understandable routines over abstraction-heavy designs.
Indirect dispatch, deep state machines, generated code, or machine-language
helpers MUST NOT be introduced unless the plan documents a measurable memory,
speed, or maintainability benefit that plain BASIC cannot provide.

Rationale: The 6510 CPU is slow, debugging tools are limited, and future edits
must remain tractable inside a BASIC codebase.

### IV. 1541-Aware Persistence

All persistence work MUST document the file format, access pattern, and error
handling for 1541-compatible storage before implementation. Read and write paths
MUST minimize disk operations, preserve recoverable record structure, and define
behavior for missing, damaged, or partially written data. Features SHOULD batch
disk access when that reduces user-visible latency without risking data loss.

Rationale: The 1541 floppy drive is the default storage target and is slow
enough that careless I/O design will dominate runtime behavior.

### V. Emulator or Hardware Validation

Every feature MUST include a manual validation procedure that can be executed in
an emulator or on real hardware. The validation path MUST cover the primary
screen flow, relevant memory and string limits, the intended disk access path,
and at least one failure or recovery scenario. Automated checks are welcome but
MUST complement, not replace, an observable end-to-end run.

Rationale: In this environment, correctness is proven by what the user can run
and observe, not by abstractions alone.

## Platform Constraints

- The primary implementation language MUST be Commodore 64 BASIC V2.
- Features MUST assume a 40x25 text screen, keyboard-driven input, and a slow
  6510 CPU unless a specification explicitly grants an exception.
- Data storage MUST be designed for 1541-compatible floppy workflows.
- Build and packaging work MUST preserve a runnable `.prg` artifact that can be
  executed in the established toolchain.

## Workflow & Review

- Specifications MUST describe the target screen states, key-driven navigation,
  and manual validation path for each user story.
- Implementation plans MUST record the memory budget, record layout, and disk
  access strategy before design is considered ready.
- Task lists MUST include work for screen flow, storage format, disk error
  handling, and manual validation whenever those concerns are in scope.
- Reviews MUST reject changes that add complexity without a stated benefit under
  the platform constraints above.

## Governance

This constitution overrides conflicting guidance in repository templates and
planning artifacts. Amendments MUST update this document and any affected
Speckit templates in the same change. Compliance reviews for specs, plans,
tasks, and implementation changes MUST verify memory budgeting, screen design,
storage strategy, and validation coverage against these principles.

Versioning policy follows semantic versioning for governance changes: MAJOR for
backward-incompatible principle removals or redefinitions, MINOR for new
principles or materially expanded obligations, and PATCH for clarifications that
do not change project obligations.

**Version**: 1.0.0 | **Ratified**: 2026-04-26 | **Last Amended**: 2026-04-26
