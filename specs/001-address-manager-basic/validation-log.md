# Validation Log: C64 Address Manager

**Feature**: Address Manager Basic (specs/001-address-manager-basic)\
**Tester**: ___________\
**Hardware/Emulator**: ___________\
**Build**: `build/AddressManagerBasic.prg`

---

## User Story 1 — Neue Adresse erfassen (P1 MVP)

**Date**: ___________\
**Duration**: ___________ (target ≤2 min from menu to saved record)

| # | Checkpoint                         | Expected                           | Actual | Pass/Fail | Notes |
| - | ---------------------------------- | ---------------------------------- | ------ | --------- | ----- |
| 1 | Main menu visible after load       | ≤30 sec; 3 options shown           |        | [ ]       |       |
| 2 | Press `1` → Suchmaske              | "NACHNAME:" prompt visible         |        | [ ]       |       |
| 3 | Press `N` → Erfassungsmaske        | 6-field form opens                 |        | [ ]       |       |
| 4 | Fill all fields + press `S`        | "GESPEICHERT"; ≤2 min total        |        | [ ]       |       |
| 5 | Restart; search saved Nachname     | Record found                       |        | [ ]       |       |
| 6 | Empty Nachname + press `S`         | Retry prompt; no disk write        |        | [ ]       |       |
| 7 | Optional fields blank + press `S`  | Save succeeds; blank fields stored |        | [ ]       |       |
| 8 | Fill fields; press `A` (Abbrechen) | Return to Suchmaske; not saved     |        | [ ]       |       |

**US1 Result**: PASS / FAIL\
**Notes**: ___________

---

## User Story 2 — Vorhandene Adresse suchen und verwalten (P2)

**Date**: ___________\
**Duration**: ___________

| # | Checkpoint                             | Expected                              | Actual | Pass/Fail | Notes |
| - | -------------------------------------- | ------------------------------------- | ------ | --------- | ----- |
| 1 | Search "Schmidt" (2 records)           | Both shown, numbered 1+2              |        | [ ]       |       |
| 2 | Select `1` → Erfassungsmaske prefilled | All fields populated                  |        | [ ]       |       |
| 3 | Edit Telefon; press `S`                | "GESPEICHERT"; change persists        |        | [ ]       |       |
| 4 | Search "Schmidt"; copy record 2        | New entry with same fields (editable) |        | [ ]       |       |
| 5 | Modify Vorname on copy; press `S`      | Third Schmidt in list                 |        | [ ]       |       |
| 6 | Delete record; press `N` at confirm    | Record unchanged                      |        | [ ]       |       |
| 7 | Delete record; press `J` at confirm    | "GELOESCHT"; record gone              |        | [ ]       |       |
| 8 | Search non-existent name               | "KEINE TREFFER" message               |        | [ ]       |       |
| 9 | I/O error simulation (detach disk)     | Error message; old data intact        |        | [ ]       |       |

**US2 Result**: PASS / FAIL\
**Notes**: ___________

---

## User Story 3 — Gesamtliste anzeigen (P3)

**Date**: ___________\
**Duration**: ___________

| # | Checkpoint                         | Expected                       | Actual | Pass/Fail | Notes |
| - | ---------------------------------- | ------------------------------ | ------ | --------- | ----- |
| 1 | Press `2` from main menu           | Gesamtliste screen opens       |        | [ ]       |       |
| 2 | Records shown in storage order     | No sorting applied             |        | [ ]       |       |
| 3 | 8 records per page                 | Page 1 shows max 8             |        | [ ]       |       |
| 4 | Press `W` (Weiter) → next page     | Page 2 shown; correct records  |        | [ ]       |       |
| 5 | Press `Z` (Zurück) → previous page | Page 1 shown again             |        | [ ]       |       |
| 6 | Press `M` → Hauptmenü              | Main menu visible              |        | [ ]       |       |
| 7 | Empty database → open full list    | "KEINE ADRESSEN" message shown |        | [ ]       |       |

**US3 Result**: PASS / FAIL\
**Notes**: ___________

---

## Full 9-Phase Validation

**Date**: ___________\
**Tester**: ___________

| Phase | Description                   | Pass/Fail | Time | Notes |
| ----- | ----------------------------- | --------- | ---- | ----- |
| 1     | Main menu navigation          | [ ]       |      |       |
| 2     | Create new address            | [ ]       |      |       |
| 3     | Search & retrieve             | [ ]       |      |       |
| 4     | Edit & copy                   | [ ]       |      |       |
| 5     | Multiple matches + pagination | [ ]       |      |       |
| 6     | Delete with confirmation      | [ ]       |      |       |
| 7     | Full list view                | [ ]       |      |       |
| 8     | Error & recovery scenarios    | [ ]       |      |       |
| 9     | Full workflow end-to-end      | [ ]       |      |       |

**Final Result**: PASS / FAIL\
**Sign-off**: ___________\
**Date**: ___________
