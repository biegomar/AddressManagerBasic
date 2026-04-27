# AddressManagerBasic

A keyboard-driven address manager for the Commodore 64, written in BASIC V2.
Stores up to ~200 contacts on a 1541-compatible floppy disk.

---

## Build (VS64 Toolchain)

1. Open this workspace in VS Code with the **vs64** extension installed.
2. Press `Ctrl+Shift+B` (or run **Terminal → Run Build Task**) to execute the
   default build task `vs64: build project`.
3. Output: `build/AddressManagerBasic.prg`

**Manual build via VS64 CLI** (if available):

```
vs64 build
```

---

## Run in VICE Emulator

1. Install [VICE](https://vice-emu.sourceforge.io/) (version 3.x or later).
2. **Attach a blank `.d64` disk image** before starting the program (VICE menu:
   **File → Attach Disk Image → Unit 8**). The program writes `ADDRESSES.DAT` to
   this disk.
3. Load the program:
   - **VICE menu**: File → Autostart image → select
     `build/AddressManagerBasic.prg`
   - **Or**: `LOAD "ADDRESSMANAGERBASIC.PRG",8` then `RUN`

---

## Run on Real Commodore 64

1. Transfer `build/AddressManagerBasic.prg` to a 1541 floppy disk.
2. Insert disk, then at the BASIC prompt:
   ```
   LOAD "ADDRESSMANAGERBASIC.PRG",8
   RUN
   ```

---

## Controls

| Key     | Action                             |
| ------- | ---------------------------------- |
| `1`     | Eintrag verwalten (go to search)   |
| `2`     | Gesamtliste anzeigen (full list)   |
| `Q`     | Beenden (quit)                     |
| `N`     | Neuer Eintrag (from search screen) |
| `S`     | Speichern (save form)              |
| `A`     | Abbrechen (cancel/discard)         |
| `J`     | Ja — confirm delete                |
| `N`     | Nein — cancel delete               |
| `W`     | Weiter (next page)                 |
| `Z`     | Zurück (previous page)             |
| `M`     | Hauptmenü (return to main menu)    |
| `1`–`8` | Select result from list            |

---

## Data Storage

Records are stored in `ADDRESSES.DAT` on device 8 (unit 8), as a pipe-separated
sequential text file:

```
NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON
```

Fields `EMAIL`, `STRASSE`, `ORT`, and `TELEFON` are optional (stored as empty
string between pipes). `NACHNAME` and `VORNAME` are mandatory.

---

## Validation

See `specs/001-address-manager-basic/quickstart.md` for the full 9-phase manual
validation workflow and test data set. Log results in
`specs/001-address-manager-basic/validation-log.md`.
