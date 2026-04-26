# Implementation Plan: C64 Address Manager

**Branch**: `002-address-manager-basic` | **Date**: 2026-04-26 | **Spec**:
[spec.md](spec.md)\
**Input**: Feature specification from `specs/001-address-manager-basic/spec.md`

**Note**: This plan was filled in by the `/speckit.plan` workflow. See
`.specify/templates/plan-template.md` for the execution workflow.

---

## Summary

**Feature**: A single-program C64 BASIC address manager with menu-driven
interface, search-by-last-name lookup, entry editing/copying/deletion, full-list
display, and persistent 1541-disk storage.

**Primary Value**: Users can maintain a small but functional contact database
(up to ~200 records) on a Commodore 64 with keyboard input and floppy-based
persistence.

**Technical Approach**: Single monolithic BASIC program (`src/main.bas`) with
pipe-separated record format, sequential file I/O, screen-state-machine
navigation, and error handling via ONERR.

---

## Technical Context

**Language/Version**: Commodore 64 BASIC V2\
**Primary Dependencies**: VS64 toolchain, C64 BASIC/KERNAL runtime,
1541-compatible disk workflow\
**Storage**: Sequential or random-access files on 1541-compatible floppy media\
**Validation**: Manual emulator (VICE) or hardware walkthroughs; automated
checks only when explicitly added\
**Target Platform**: Commodore 64 or a faithful emulator with 40x25 text
display\
**Project Type**: Single-program retro application\
**Performance Goals**: Acceptable interactive response despite BASIC interpreter
and 1541 I/O latency\
**Constraints**: 64 KB shared memory, 255-character string limit, 40x25 screen,
slow 6510 CPU, slow floppy I/O\
**Scale/Scope**: Single-user utility with bounded record counts (~200) and
intentionally compact workflows

---

## Constitution Check

_GATE: Must pass before Phase 0 research. Re-check after Phase 1 design._

✅ **All gates PASS**:

- ✅ **Memory budget documented**: Total program ~15–20 KB, runtime buffers ~5
  KB, well within 64 KB limit. No multi-string records needed (all records <255
  chars). See [data-model.md](data-model.md).
- ✅ **Screen states & navigation mapped**: 7 distinct 40×25 screen states
  defined. Each has one purpose. Keyboard-driven navigation documented. See
  [data-model.md#4-bildschirmzustand](data-model.md#4-bildschirmzustand) and
  state-transition diagram.
- ✅ **1541 storage strategy**: Pipe-separated records in sequential file
  format. Max ~200 records. Error handling via ONERR. See
  [data-model.md#storage-format](data-model.md#storage-format) and
  [research.md#2-1541-floppy](research.md#2-1541-floppy).
- ✅ **Manual validation path**: Full 9-phase validation workflow documented in
  [quickstart.md](quickstart.md), covering all user stories, error scenarios,
  and end-to-end integration.
- ✅ **Complexity justified**: No complexity beyond straightforward BASIC
  control flow. Pipe parsing and screen state machine are standard BASIC
  patterns. No machine language, no indirect dispatch, no generated code.

**Constitutional Compliance**: Feature complies with Constitution 1.0.0
(ratified 2026-04-26). No violations or special justifications required.

---

## Project Structure

### Documentation (this feature)

```text
specs/001-address-manager-basic/
├── spec.md                          # Feature specification (5 clarifications integrated)
├── plan.md                          # This file (implementation planning)
├── research.md                      # Phase 0: Technical research (C64 BASIC, 1541, screen I/O)
├── data-model.md                    # Phase 1: Data storage format, entities, state machine
├── quickstart.md                    # Phase 1: Manual validation workflow (9 phases)
├── validation-log.md                # Validation evidence log for manual test runs
├── contracts/
│   └── runtime-interface.md         # Phase 1: Runtime contract for BASIC program
├── checklists/
│   ├── requirements.md              # Initial quality checklist
│   └── requirements-clarified.md    # Clarification session results (Q1–Q5)
└── tasks.md                         # Phase 2 output (NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
src/
└── main.bas                         # Single BASIC program (all feature code)

build/
├── AddressManagerBasic.prg          # Compiled output (runnable on C64)
├── AddressManagerBasic.bmap         # Build artifacts
└── build.ninja                      # Build configuration (VS64 toolchain)

req/
└── Req.md                           # Original user requirements (German)

specs/001-address-manager-basic/    # This feature's planning artifacts
```

**Structure Decision**: Keep all feature code in `src/main.bas` (single
program). No modularization or helper files required. Build produces
`AddressManagerBasic.prg` which runs standalone with disk-based persistence.

---

## Data Model Summary

**Record Format**: Pipe-separated string
(NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON)\
**Field Lengths**: Nachname (30), Vorname (30), Email (40), Strasse (40), Ort
(30), Telefon (20)\
**Max Record Size**: ~210 characters (all fields + 5 pipes) → **Fits in 255-char
BASIC string**\
**Storage**: `ADDRESSES.DAT` file on 1541 disk (sequential format)\
**Max Records**: ~200 (practical limit for C64 BASIC + 1541 performance)

**Screen States** (7 total):

1. Hauptmenu (Main menu)
2. Suchmaske (Search input)
3. Trefferliste (Search results, numbered 1–N)
4. Erfassungsmaske (Entry form for create/edit)
5. Gesamtliste (Full address list, paginated)
6. Loeschbestaetigung (Delete confirmation)
7. Meldungsbildschirm (Status/error messages)

**State Machine**: User navigates via single-key selection; all transitions
documented in [data-model.md](data-model.md).

---

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

| Violation         | Why Needed | Simpler Alternative Rejected Because |
| ----------------- | ---------- | ------------------------------------ |
| _(No violations)_ | _(N/A)_    | _(Constitution gates all pass)_      |

---

## Technical Risks & Mitigations

| Risk                            | Impact               | Mitigation                                                              |
| ------------------------------- | -------------------- | ----------------------------------------------------------------------- |
| BASIC string parsing complexity | Code maintainability | Simple INSTR() + MID$() patterns; document with inline comments         |
| 1541 I/O latency                | User experience      | Accept 5-second search timeout; document in success criteria            |
| 64 KB memory ceiling            | Feature scope        | Limit to 200 records; keep only active record in RAM; rest on disk      |
| No automatic error recovery     | User experience      | Implement ONERR + user-facing messages; accept manual retry             |
| Screen real estate (40×25)      | Usability            | Design 7 simple single-purpose screens; short prompts; no dense layouts |

---

## Success Criteria Alignment

| Criterion                               | Status | Evidence                                                             |
| --------------------------------------- | ------ | -------------------------------------------------------------------- |
| SC-001: Menu recognized within 30 sec   | ✅     | Main menu is first screen; label text clear                          |
| SC-002: Entry creation <2 minutes       | ✅     | 6 fields with reasonable defaults; keyboard input only               |
| SC-003: Search <5 seconds (normal disk) | ✅     | Sequential scan of ~200 records ≈ 1541 disk speed limit              |
| SC-004: Full workflows in one session   | ✅     | All 3 user stories (create, search/edit, list) runnable sequentially |
| SC-005: Error recovery preserves data   | ✅     | ONERR handler + old data buffered in RAM before save attempt         |

---

## Known Limitations & Out-of-Scope

### In Scope v1:

- ✅ Keyboard-driven menu navigation
- ✅ Pipe-separated record format
- ✅ Exakt search by last name (no wildcards)
- ✅ Sequential file I/O (1541)
- ✅ Create, edit, copy, delete operations
- ✅ Manual emulator/hardware validation
- ✅ Simple error messages

### Out of Scope v1:

- ❌ Sorting or filtering (results in storage order)
- ❌ Joystick input (keyboard only)
- ❌ Wildcard or prefix search
- ❌ Data export/import
- ❌ Multi-record selection or bulk operations
- ❌ Graphics or color formatting
- ❌ Automatic disk formatting or recovery

---

## Next Steps (After Plan Approval)

1. **`/speckit.tasks`**: Generate detailed task list from this plan +
   data-model + spec
2. **`/speckit.implement`**: Execute task implementation in `src/main.bas`
3. **Manual Validation**: Follow [quickstart.md](quickstart.md) workflow on
   VICE + real hardware
4. **Delivery**: Commit final `.prg` artifact + validation checklist

---

## Appendix: Technical Decisions

### Why Pipe Separator (not comma)?

- Comma is common in names/addresses (e.g., "Smith, John"); pipe is rare in
  natural text
- Easier to INSTR() and MID$() for parsing in BASIC

### Why Sequential File (not indexed)?

- REL-file random access adds complexity; sequential scan is acceptable for 200
  records
- C64 BASIC has simpler sequential I/O; fewer error cases

### Why Single-Program Design?

- Memory constraints (39 KB BASIC space). Modularization overhead > benefits.
- CALL or cross-program coordination adds complexity without benefit for single
  user.

### Why No Automatic Sorting?

- Sorting 200 records in BASIC is slow and RAM-intensive
- Storage order is predictable; users can re-insert records in desired order if
  needed
- Acceptable for MVP; can extend later if needed

### Why Exact Search Only?

- Prefix matching requires loop + string comparison in BASIC; adds latency
- Users can retry with full or partial name
- Acceptable for bounded dataset

---

**Version**: 1.0.0 | **Status**: Ready for Task Generation | **Validated**:
2026-04-26
