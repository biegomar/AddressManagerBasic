# Contract: Address Manager BASIC Program Interface

**Purpose**: Define the runtime interface contract for the C64 Address Manager\
**Type**: Single-program BASIC application (no external interfaces)\
**Created**: 2026-04-26

## Entry Point

**Program**: `src/main.bas` (compiled to `build/AddressManagerBasic.prg`)

### Startup Behavior

- Load: `LOAD "ADDRESSMANAGERBASIC.PRG", 8`
- Run: `RUN`
- Entry: Main menu screen displayed
- No external initialization required

### Exit Behavior

- User selects "Beenden" (Exit) from main menu
- Program terminates cleanly
- Data persisted to `ADDRESSES.DAT` on disk
- Return to BASIC prompt or emulator menu

---

## Runtime Contract

### Input Interface

**Keyboard Input** (only):

- Single key presses for menu selection (1, 2, 3, Q, etc.)
- Text entry in forms (Alphanumeric + ASCII characters)
- No joystick or mouse input

### Output Interface

**Screen** (40×25 text display):

- Text-only, no graphics
- Color optional (if supported by terminal)
- 7 distinct screen states (see data-model.md)

### Data Persistence

**Storage**: `ADDRESSES.DAT` on 1541-compatible floppy

- Format: Pipe-separated records (one record per line)
- Structure: `NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON`
- Encoding: C64 ASCII/PETSCII
- Max record size: 255 characters
- Max file size: ~200 records

### Error Handling Contract

**I/O Errors**:

- Detected via ONERR handler
- User-facing message: "FEHLER #[N]"
- Graceful recovery: Return to previous menu or main menu
- Data protection: Old data preserved in memory until new save confirmed

**Input Validation**:

- Pipe characters (|) forbidden in field content
- Newline characters forbidden in field content
- Max field lengths enforced at input time

---

## Performance Contract

**Response Times**:

- Menu selection: <100ms
- Search operation (100 records): <5 seconds
- Record save (I/O): ~1–2 seconds (1541 disk speed)
- Record display: <500ms

**Memory Contract**:

- Estimated BASIC program size: ~15–20 KB
- Runtime variables: ~5 KB (current record + buffers)
- Total RAM used: <25 KB (well within 64 KB limit)

---

## Backwards Compatibility

Not applicable (first version; no legacy data formats).

---

## Future Extensions (Out of Scope v1)

- Sorting by field
- Joystick input
- Wildcard search
- Data export/import
- Multi-record display
