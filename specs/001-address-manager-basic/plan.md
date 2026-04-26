# Implementation Plan: C64 Address Manager

**Branch**: `002-address-manager-basic` | **Date**: 2026-04-26 | **Spec**:
[spec.md](spec.md)\
**Input**: Feature specification from `specs/001-address-manager-basic/spec.md`\
**Clarifications**: Session 2026-04-26 (original) + Session 2026-04-26
(Refinement) — 10 decisions total, all integrated.

---

## Summary

**Feature**: A single-program C64 BASIC address manager with keyboard-driven
menu interface, exact last-name search, entry creation/editing/copying/deletion,
paginated full-list display, and sequential 1541-disk persistence.

**Primary Value**: Users can maintain a small contact database (~200 records) on
a Commodore 64 with keyboard input and floppy-based persistence.

**Technical Approach**: Single monolithic BASIC program (`src/main.bas`) with
pipe-separated record format, sequential file I/O (`ADDRESSES.DAT`),
screen-state-machine navigation across 7 screens, ONERR-based error handling,
and fixed 8-entries-per-page pagination. New entries are created via Eintrag
verwalten → Taste N.

---

## Technical Context

**Language/Version**: Commodore 64 BASIC V2\
**Primary Dependencies**: VS64 toolchain, C64 BASIC/KERNAL runtime,
1541-compatible disk workflow\
**Storage**: Sequential file with sequential read/write on 1541-compatible
floppy media (MVP; no random-access required)\
**Validation**: Manual emulator (VICE) or hardware walkthroughs; automated
checks only when explicitly added\
**Target Platform**: Commodore 64 or a faithful emulator with 40×25 text
display\
**Project Type**: Single-program retro application\
**Performance Goals**: Menu <100 ms, search <5 sec, save ~1–2 sec (1541 speed)\
**Constraints**: 64 KB shared memory, 255-character string limit, 40×25 screen,
slow 6510 CPU, slow floppy I/O\
**Scale/Scope**: Single-user utility, max ~200 records, 8 entries per page

---

## Constitution Check

_GATE: Must pass before Phase 0 research. Re-check after Phase 1 design._

✅ **All gates PASS** (re-verified after Refinement session 2026-04-26):

- ✅ **Memory budget documented**: Program ~15–20 KB, runtime buffers ~5 KB
  (current record + 8-entry page buffer). Total <25 KB within 39 KB BASIC space.
  All records fit in a single 255-char string (~190 chars actual). See
  [data-model.md](data-model.md).
- ✅ **Screen states & navigation mapped**: 7 distinct 40×25 screen states, each
  with one purpose and visible key choices. New entry flow: Eintrag verwalten →
  Taste N → Erfassungsmaske. See [data-model.md](data-model.md).
- ✅ **1541 storage strategy**: Sequential file `ADDRESSES.DAT` with sequential
  I/O. Pipe-separated records. Error handling via ONERR; on failure return to
  previous screen with old data intact. See [data-model.md](data-model.md),
  [research.md](research.md).
- ✅ **Manual validation path**: 9-phase workflow in
  [quickstart.md](quickstart.md) covers all user stories, timed checks (SC-001
  ≤30 sec, SC-002 ≤2 min), disk error simulation, and abort scenarios.
- ✅ **Complexity justified**: No machine language, no indirect dispatch, no
  random-access file logic. Pipe parsing with INSTR/MID$ is standard BASIC.

**Constitutional Compliance**: Complies with Constitution 1.0.0 (2026-04-26). No
violations.

---

## Project Structure

### Documentation (this feature)

```text
specs/001-address-manager-basic/
├── spec.md                        # Feature spec (10 clarifications integrated)
├── plan.md                        # This file
├── research.md                    # Phase 0: C64 BASIC, 1541 I/O, memory layout
├── data-model.md                  # Phase 1: Record format, entities, state machine
├── quickstart.md                  # Phase 1: 9-phase manual validation workflow
├── validation-log.md              # Validation evidence log (created during testing)
├── contracts/
│   └── runtime-interface.md       # Runtime contract: I/O, persistence, performance
├── checklists/
│   ├── requirements.md
│   └── requirements-clarified.md
└── tasks.md                       # Phase 2: 49 tasks across 6 phases
```

### Source Code (repository root)

```text
src/
└── main.bas                       # Single BASIC program (all feature code)

build/
├── AddressManagerBasic.prg        # Compiled output (runnable on C64)
├── AddressManagerBasic.bmap
└── build.ninja
```

**Structure Decision**: All feature code stays in `src/main.bas`. No additional
`.bas` files or helper assets needed.

---

## Data Model Summary

**Record format**: `NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON`\
**Required fields**: Nachname, Vorname (mandatory); Email, Strasse, Ort, Telefon
(optional — stored as empty string between pipes if omitted)\
**Field lengths**: Nachname 30, Vorname 30, Email 40, Strasse 40, Ort 30,
Telefon 20\
**Max record size**: ~195 chars (fields + 5 pipes) — fits in 255-char BASIC
string with reserve\
**Storage file**: `ADDRESSES.DAT`, sequential format, 1541-compatible\
**Max records**: ~200\
**Page size**: 8 entries per page (Trefferliste and Gesamtliste)

**Screen states** (7 total):

1. Hauptmenü — Eintrag verwalten | Gesamtliste anzeigen | Beenden
2. Suchmaske — exact Nachname input; Taste N → new entry
3. Trefferliste — numbered 1–N, max 8 per page
4. Erfassungsmaske — 6-field form (create or edit); Speichern / Abbrechen
5. Gesamtliste — all records paginated, 8 per page
6. Löschbestätigung — J/N; N returns to previous screen
7. Meldungsbildschirm — success or error; error returns to previous screen

---

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
| --------- | ---------- | ------------------------------------ |
| _(none)_  | —          | All gates pass; no violations        |

---

## Technical Risks & Mitigations

| Risk                     | Impact          | Mitigation                                  |
| ------------------------ | --------------- | ------------------------------------------- |
| BASIC string parsing     | Maintainability | INSTR/MID$ patterns; inline comments        |
| 1541 I/O latency         | UX              | Accept ≤5 sec search; document in SC-003    |
| 64 KB memory ceiling     | Scope           | 200-record cap; only active record in RAM   |
| No auto error recovery   | UX              | ONERR + message + return to previous screen |
| Screen real estate 40×25 | Usability       | 7 single-purpose screens; 8-entry pages     |

---

## Success Criteria Alignment

| Criterion                       | Status | Evidence                                           |
| ------------------------------- | ------ | -------------------------------------------------- |
| SC-001: Menu recognised ≤30 sec | ✅     | First screen is main menu; timed check in T019     |
| SC-002: Entry creation ≤2 min   | ✅     | 6 fields, 2 mandatory; timed check in T019         |
| SC-003: Search result ≤5 sec    | ✅     | Sequential scan of ≤200 records within 1541 speed  |
| SC-004: Full session workflow   | ✅     | All 3 stories runnable in one session (T047)       |
| SC-005: Error recovery intact   | ✅     | ONERR + previous-screen return + old data buffered |

---

## Known Limitations & Out-of-Scope (v1)

- ❌ Sorting or filtering (results in storage order)
- ❌ Joystick input
- ❌ Wildcard or prefix search
- ❌ Data export/import
- ❌ Automatic disk formatting or recovery
- ❌ Multi-record selection

---

## Appendix: Technical Decisions

| Decision          | Choice                | Rationale                                                      |
| ----------------- | --------------------- | -------------------------------------------------------------- |
| Field separator   | Pipe `\|`             | Comma appears in addresses; pipe is rare in natural text       |
| File access       | Sequential            | Simpler I/O; ≤200 sequential records acceptable                |
| Record multi-part | No                    | All records ≤195 chars; 255-char limit not reached             |
| Sorting           | None                  | Sorting 200 records in BASIC is slow; storage order acceptable |
| Search matching   | Exact only            | Prefix matching adds latency; acceptable for bounded set       |
| Create entry path | Eintrag verwalten → N | Keeps menu lean; manage+create logically grouped               |
| Page size         | 8 fixed               | Consistent UX across Trefferliste and Gesamtliste              |
| Error return      | Previous screen       | Context preserved; less disorienting than forced main menu     |
| Mandatory fields  | Nachname + Vorname    | Minimum viable contact; other fields situational               |

---

**Version**: 1.1.0 | **Status**: Ready for Implementation | **Updated**:
2026-04-26 (Refinement clarifications applied)
