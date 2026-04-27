# Quickstart & Manual Validation: C64 Address Manager

**Purpose**: Step-by-step instructions to validate the Address Manager feature
on emulator or real hardware\
**Target Environment**: VICE emulator (recommended for development) or real
Commodore 64 + 1541 disk\
**Estimated Duration**: 10–15 minutes per validation cycle

---

## Test Data Set

Use these 10 sample contacts for validation. They include duplicate last names
and a mix of complete / partial records to exercise all code paths.

| #  | Nachname | Vorname | Email                 | Strasse       | Ort       | Telefon     |
| -- | -------- | ------- | --------------------- | ------------- | --------- | ----------- |
| 1  | Schmidt  | Hans    | hans.schmidt@test.de  | Hauptstr. 1   | Berlin    | 030-111111  |
| 2  | Schmidt  | Peter   | peter.schmidt@test.de | Bahnhofstr. 5 | Hamburg   | 040-222222  |
| 3  | Mueller  | Anna    | anna.mueller@test.de  | Gartenweg 3   | Muenchen  | 089-333333  |
| 4  | Mueller  | Klaus   |                       | Ringstr. 9    | Koeln     |             |
| 5  | Wagner   | Maria   | maria.wagner@test.de  |               | Frankfurt | 069-444444  |
| 6  | Becker   | Thomas  |                       |               |           | 0221-555555 |
| 7  | Hoffmann | Lisa    | lisa.h@test.de        | Dorfstr. 7    | Stuttgart | 0711-666666 |
| 8  | Fischer  | Georg   | g.fischer@test.de     | Marktplatz 2  | Nuernberg | 0911-777777 |
| 9  | Richter  | Eva     |                       | Bergweg 11    | Dresden   |             |
| 10 | Schulz   | Werner  | werner.schulz@test.de | Seestr. 4     | Bremen    | 0421-888888 |

**Notes**:

- Records 1+2 and 3+4 share the same Nachname → test duplicate search handling
- Records 4, 6, 9 have optional fields empty → test optional field save/load
- Enter all 10 records manually during Phase 2 of validation (or load from a
  pre-formatted `ADDRESSES.DAT` if available)

---

## US1 Validation Checklist

**User Story 1**: Neue Adresse erfassen (Priority P1, MVP)

**Timed checks** (measure with a clock or stopwatch):

| # | Step                                             | Expected                            | Timed? | Pass/Fail |
| - | ------------------------------------------------ | ----------------------------------- | ------ | --------- |
| 1 | Load program; read main menu                     | All 3 options visible in ≤30 sec    | ≤30 s  | [ ]       |
| 2 | Press `1` → Suchmaske appears                    | Prompt "NACHNAME:" visible          | —      | [ ]       |
| 3 | Press `N` → Erfassungsmaske opens                | 6 fields shown (Nachname first)     | —      | [ ]       |
| 4 | Fill all 6 fields; press `S`                     | "GESPEICHERT" message; ≤2 min total | ≤2 min | [ ]       |
| 5 | Auto-return or press key → main menu             | Main menu visible                   | —      | [ ]       |
| 6 | Restart program; search for saved Nachname       | Record still present                | —      | [ ]       |
| 7 | Start new entry; leave Nachname empty; press `S` | Error/retry prompt; no save         | —      | [ ]       |
| 8 | Start new entry; fill only Nachname + Vorname    | Save succeeds; other fields blank   | —      | [ ]       |
| 9 | Start new entry; fill fields; press `A`          | Return to Suchmaske; nothing saved  | —      | [ ]       |

---

## US2 Validation Checklist

**User Story 2**: Vorhandene Adresse suchen und verwalten (Priority P2)

| # | Step                                                  | Expected                                     | Pass/Fail |
| - | ----------------------------------------------------- | -------------------------------------------- | --------- |
| 1 | Search "Schmidt" (with 2 records existing)            | Both shown, numbered 1 and 2                 | [ ]       |
| 2 | Select result by pressing `1`                         | Detail view with all fields shown            | [ ]       |
| 3 | Press `B` (Bearbeiten) → edit Telefon → press `S`     | "GESPEICHERT"; change persists after restart | [ ]       |
| 4 | Search "Schmidt", select record, press `K` (Kopieren) | New form pre-filled with same data           | [ ]       |
| 5 | Modify Vorname to "Peter" → press `S`                 | Third Schmidt visible in search results      | [ ]       |
| 6 | Select a record; press `L` → confirm screen appears   | "WIRKLICH LOESCHEN? J/N"                     | [ ]       |
| 7 | Press `N` at delete confirm                           | Record unchanged; back to results list       | [ ]       |
| 8 | Press `L` again, then `J`                             | "GELOESCHT"; record gone from search         | [ ]       |
| 9 | Search for non-existent name (e.g., "Zzz")            | "KEINE TREFFER FUER: Zzz" message            | [ ]       |

---

## US3 Validation Checklist

**User Story 3**: Gesamtliste anzeigen (Priority P3)

| # | Step                               | Expected                               | Pass/Fail |
| - | ---------------------------------- | -------------------------------------- | --------- |
| 1 | From main menu press `2`           | Gesamtliste opens with first 8 records | [ ]       |
| 2 | Records shown in storage order     | Same order as entered (no sorting)     | [ ]       |
| 3 | If >8 records: press `W` (Weiter)  | Page 2 shown; page indicator updates   | [ ]       |
| 4 | Press `Z` (Zurück)                 | Back to page 1                         | [ ]       |
| 5 | Press `M` (Hauptmenü)              | Main menu visible                      | [ ]       |
| 6 | Delete all records; open full list | "KEINE ADRESSEN VORHANDEN." message    | [ ]       |

---

1. **VICE Emulator** (https://vice-emu.sourceforge.io/) or **Real C64 + 1541
   Floppy Drive**
2. **Compiled `.prg` file**: `build/AddressManagerBasic.prg`
3. **Blank disk image** or **formatted 1541 disk** for data storage
4. **Text editor** for manual record creation (optional; can test via program)

---

## Setup: VICE Emulator

### Quick Start in VICE

1. **Launch VICE**:
   - Open VICE 64 emulator
   - In menu: **File → Autostart image** → Select
     `build/AddressManagerBasic.prg`
   - OR **File → Attach Disk Image (Unit 8)** → Attach a blank disk image first

2. **Attach a blank disk** (if not auto-attached):
   - In VICE menu: **File → Attach Disk Image (Unit 8)**
   - Select or create a `.d64` file (blank 1541 disk image)
   - Click **Attach**

3. **Start the program**:
   - In VICE: **File → Autostart image** → Select
     `build/AddressManagerBasic.prg`
   - Program loads and displays main menu

### Setup: Real Commodore 64

1. Boot Commodore 64
2. Insert 1541 disk with `AddressManagerBasic.prg`
3. Type: `LOAD "ADDRESSMANAGERBASIC.PRG", 8`
4. Press **RUN**

---

## Validation Workflow: Full Feature Test

### Phase 1: Main Menu Navigation

**Objective**: Verify main menu displays and accepts key input\
**Duration**: 1 minute

**Steps**:

1. Start program (either emulator or real hardware)
2. **Expected**: See main menu with 3 options:
   - `1. EINTRAG VERWALTEN` (Manage Entry)
   - `2. LISTE ANZEIGEN` (Show List)
   - `Q. BEENDEN` (Exit)
3. Press `2` → Should navigate to "Show List" screen (empty initially)
4. Press `Q` → Should return to main menu
5. **Validation**: Menu navigation works; key input is responsive

---

### Phase 2: Create New Address (User Story 1)

**Objective**: Create, save, and retrieve a new address\
**Duration**: 3–4 minutes

**Steps**:

1. **From main menu, press `1`** (Manage Entry)
   - Expected: See search screen with prompt "NACHNAME:" (Last Name)

2. **Press `N`** (for "New") or similar key to create new entry (if implemented)
   - Alternative: If no "New" key, skip to Phase 3 first, then return here
   - Expected: See entry form with 6 fields:
     - Nachname (Last Name)
     - Vorname (First Name)
     - Email
     - Strasse (Street)
     - Ort (City)
     - Telefon (Phone)

3. **Fill in fields** (example):
   - Nachname: `Schmidt`
   - Vorname: `Hans`
   - Email: `hans@test.com`
   - Strasse: `Hauptstr. 1`
   - Ort: `Berlin`
   - Telefon: `030-123456`

4. **Press `S`** (Save) or Enter to confirm
   - Expected: "GESPEICHERT" (Saved) message appears
   - Disk activity visible in VICE or on real hardware

5. **Return to main menu** (auto-navigate or press key)
   - **Validation**: New record created and persisted ✓

---

### Phase 3: Search & Retrieve Address (User Story 2 – Part 1)

**Objective**: Search by last name, retrieve matching records\
**Duration**: 2–3 minutes

**Steps**:

1. **From main menu, press `1`** (Manage Entry)
   - Expected: See search screen "NACHNAME:"

2. **Type the last name** used in Phase 2: `Schmidt`
   - Press **Enter**
   - Expected: Search result screen shows:
     ```
     SUCHERGEBNIS:
     1. SCHMIDT, HANS
     [weitere Optionen]
     ```
   - **Validation**: Record found and displayed with sequential numbering ✓

3. **Press `1`** to select first result
   - Expected: Current record is loaded; display or confirmation screen

4. **Verify record fields** match what you entered in Phase 2
   - **Validation**: All fields retrieved correctly ✓

---

### Phase 4: Edit & Copy Address (User Story 2 – Part 2)

**Objective**: Modify an address, save changes, and create a copy\
**Duration**: 3–4 minutes

**Steps**:

1. **With current record selected** (from Phase 3), press `E` (Edit) or similar
   - Expected: Edit form appears with current values pre-filled

2. **Modify one field** (e.g., change Telefon to `030-654321`)
   - Press **Enter** to confirm changes

3. **Press `S`** (Save)
   - Expected: "GEAENDERT UND GESPEICHERT" (Modified and Saved) message

4. **Search again** for `Schmidt` → verify modified phone number appears
   - **Validation**: Edit persisted correctly ✓

5. **Select the record again**, press `C` (Copy)
   - Expected: New entry form appears with current values pre-filled

6. **Modify Vorname** to `Peter` (keep last name as `Schmidt`)
   - Press **S** (Save)
   - Expected: "GESPEICHERT" message

7. **Search for `Schmidt`** again
   - Expected: TWO results now:
     ```
     1. SCHMIDT, HANS
     2. SCHMIDT, PETER
     ```
   - **Validation**: Copy created and saved; two records with same Nachname
     found ✓

---

### Phase 5: Test Multiple Matches & Pagination (Related to Phase 3)

**Objective**: Verify handling of multiple records with same last name\
**Duration**: 1–2 minutes

**Steps**:

1. **With two `Schmidt` records present**, search for `Schmidt` again
   - Expected: Both appear, sequentially numbered:
     ```
     1. SCHMIDT, HANS (with Phone)
     2. SCHMIDT, PETER (with Phone)
     ```

2. **Select each by pressing `1` or `2`**
   - Verify each shows correct details

3. **If more than 8 records appear**, test pagination (Page Down / Page Up keys)
   - **Validation**: Large result sets handled; navigation works ✓

---

### Phase 6: Delete Address (User Story 2 – Part 3)

**Objective**: Delete a record with confirmation\
**Duration**: 2 minutes

**Steps**:

1. **Search for `Schmidt`**, select result (e.g., `SCHMIDT, PETER`)

2. **Press `D`** (Delete)
   - Expected: Confirmation screen:
     ```
     WIRKLICH LOESCHEN?
     SCHMIDT, PETER
     J/N
     ```

3. **Press `N`** (No) first
   - Expected: Return to previous screen; record still exists
   - **Validation**: Cancel works; record NOT deleted ✓

4. **Repeat: Search `Schmidt`, select `SCHMIDT, PETER`, press `D`**

5. **Press `J`** (Yes)
   - Expected: "GELOESCHT" (Deleted) message

6. **Search for `Schmidt` again**
   - Expected: Only `SCHMIDT, HANS` remains
   - **Validation**: Delete confirmed; record removed ✓

---

### Phase 7: View Full List (User Story 3)

**Objective**: Display all stored addresses\
**Duration**: 1–2 minutes

**Steps**:

1. **From main menu, press `2`** (Show Full List)
   - Expected: List screen displays all addresses in storage order:
     ```
     ALLE ADRESSEN:
     1. SCHMIDT, HANS
     [page 1 of N]
     ```

2. **If many records exist**, test pagination
   - Press **Page Down** or similar to view next page
   - Press **Page Up** or similar to return

3. **Press `Q`** or **Return to Menu** key
   - Expected: Return to main menu
   - **Validation**: Full list navigable ✓

---

### Phase 8: Error & Recovery Scenarios

**Objective**: Validate error handling and data protection\
**Duration**: 2–3 minutes

#### Scenario 8a: No Matches Found

1. **Search for a non-existent last name**, e.g., `Impossible`
   - Expected: "KEINE TREFFER" (No Results) message
   - Verify menu option to return to search or main menu
   - **Validation**: Graceful handling of no-match case ✓

#### Scenario 8b: Simulate Disk I/O Error (VICE only)

1. **From within program**, delete or corrupt the disk image while a save is
   pending
   - Expected: "FEHLER #XX" (Error #XX) message appears
   - Data NOT corrupted; can continue or restart
   - **Validation**: Error message shown; old data preserved ✓

#### Scenario 8c: Abort Entry Without Saving

1. **Start creating new entry**, fill in some fields
   - Press **Escape** or **Cancel** button
   - Expected: Return to previous screen; entry NOT saved
   - **Validation**: Abort works; no accidental save ✓

---

### Phase 9: Full Workflow (Optional End-to-End Test)

**Objective**: Perform all three user stories in one session\
**Duration**: 5–10 minutes

**Simplified Steps**:

1. Create 3–5 new addresses (Phase 2)
2. Search for and edit one address (Phase 4)
3. Copy one address (Phase 4)
4. Delete one address (Phase 6)
5. View full list (Phase 7)

**Expected**: All operations complete without crashes or data loss

**Validation**: Feature is ready for delivery ✓

---

## Post-Validation Checklist

After completing all phases, verify:

- [ ] All 7 screen states are clear and navigate correctly
- [ ] Records created are persistently saved to disk
- [ ] Sequential numbering works for multiple matches
- [ ] Search is exact (no partial matches)
- [ ] Results are in storage order (no sorting applied)
- [ ] Pagination works (if applicable)
- [ ] Delete confirmation prevents accidental loss
- [ ] Error messages are readable and informative
- [ ] Abort/Cancel actions prevent unwanted saves
- [ ] Program runs in VICE emulator without crashes
- [ ] Program loads on real C64 (if available) and behaves identically

---

## Troubleshooting

| Issue                        | Cause                      | Solution                                  |
| ---------------------------- | -------------------------- | ----------------------------------------- |
| Program won't load           | Disk image corrupted       | Recreate disk image                       |
| "DEVICE NOT READY"           | VICE: Disk not attached    | Attach disk image in VICE File menu       |
| Records not saved            | I/O error silently ignored | Check VICE disk image permissions         |
| Strange characters on screen | Screen RAM corruption      | Restart program; clear screen with Ctrl+L |
| Very slow disk access        | 1541 simulation lag (VICE) | Normal; patient wait                      |
| Can't find saved record      | Typo in search name        | Search is exact; check spelling           |

---

## Final Sign-Off

**When all validation phases pass** and checklist is complete, the Address
Manager feature is **VALIDATED** and ready for delivery.

- **Validation Date**: ___________
- **Tester**: ___________
- **Hardware/Emulator**: ___________
- **Notes**: ___________
