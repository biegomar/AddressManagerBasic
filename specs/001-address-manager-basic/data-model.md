# Data Model: C64 Address Manager

**Created**: 2026-04-26\
**Phase**: 1 – Design\
**Input**: Spec (with 5 clarifications), Research findings

## Storage Format

### Record Structure: Pipe-Separated String

Each address record is stored as a single pipe-separated string (no record
delimiters beyond the newline).

```
NACHNAME|VORNAME|EMAIL|STRASSE|ORT|TELEFON
```

**Example**:

```
Schmidt|Hans|hans.schmidt@example.com|Hauptstr. 123|Berlin|030-12345678
```

**Field Specifications**:

| Feld     | Max. Länge | Typ              | Beispiel                 |
| -------- | ---------- | ---------------- | ------------------------ |
| Nachname | 30         | Alphanumerisch   | Schmidt                  |
| Vorname  | 30         | Alphanumerisch   | Hans                     |
| Email    | 40         | ASCII            | hans.schmidt@example.com |
| Strasse  | 40         | Alphanumerisch   | Hauptstr. 123            |
| Ort      | 30         | Alphanumerisch   | Berlin                   |
| Telefon  | 20         | Numerisch/Dashes | 030-12345678             |

**Total Record Length**: 6 Felder × ~max. 30–40 Zeichen + 5 Pipes = ~190–210
Zeichen → **Passt sicher in einen einzelnen 255er-String** (mit Reserve für
Leseoperationen).

### Storage Location

File: `ADDRESSES.DAT` (1541-compatible, sequenziell verarbeitet)

- Stored on bootable 1541 floppy or emulated disk
- Sequential read/write as MVP access strategy
- Each line = one record (newline-delimited internally by BASIC I/O)

### Key Constraints

1. **No multi-part records needed** – all records fit in 255 chars
2. **Pipe character (|)** used as field separator; not allowed in field content
3. **Newline (\n)** used as record separator; not allowed in field content
4. **Max. ~200 records** per file (reasonable for C64 storage + performance)

---

## Entities

### 1. Adressdatensatz (Address Record)

**Definition**: A stored contact with six standardized fields.

**Attributes**:

- Nachname: String (1–30 Zeichen)
- Vorname: String (1–30 Zeichen)
- Email: String (0–40 Zeichen, optional)
- Strasse: String (0–40 Zeichen, optional)
- Ort: String (0–30 Zeichen, optional)
- Telefon: String (0–20 Zeichen, optional)

**Constraints**:

- At least Nachname + Vorname required
- Total record ≤ 255 characters
- No pipe (|) or newline characters allowed in fields

**Persistence**: Stored in ADDRESSES.DAT as pipe-separated string.

### 2. Trefferliste (Search Result List)

**Definition**: A collection of address records matching a search criterion
(exact Nachname match).

**Attributes**:

- Eintraege: Array of Adressdatensatz references
- Anzahl: Integer (count of matches, max. ~10 on display)
- Status: "Keine Treffer" | "1 Treffer" | "N Treffer angezeigt"

**Constraints**:

- Max. 5–8 entries displayed per page
- Entries numbered 1–N for user selection
- Stored in RAM during session (not persisted between programs)

### 3. Aktueller Eintrag (Current Record Buffer)

**Definition**: The address record selected by the user from a search result.

**Attributes**:

- Nachname, Vorname, Email, Strasse, Ort, Telefon (6 string fields)
- Status: "unmodified" | "modified" | "new"

**Constraints**:

- Only one current record in memory at a time
- Persists until user closes the record or navigates away
- Modifications held in RAM until explicitly saved

### 4. Bildschirmzustand (Screen State)

**Definition**: The current displayed UI screen.

**Enumeration**:

1. **Hauptmenü** (Main Menu) – "Eintrag verwalten", "Liste anzeigen", "Beenden"
2. **Suchmaske** (Search Input) – Prompt for Nachname
3. **Trefferliste** (Search Results) – Numbered list + "Zurück" option
4. **Erfassungsmaske** (Entry Form) – Input fields for 6 fields + "Speichern" /
   "Abbrechen"
5. **Gesamtliste** (Full List) – All records, paginated with "Weiter" / "Zurück"
   / "Hauptmenu"
6. **Loeschbestaetigung** (Delete Confirmation) – "Wirklich loeschen? J/N"
7. **Meldungsbildschirm** (Message Screen) – "Gespeichert", "Gelöscht",
   "Fehler!", etc.

**Constraints**:

- Each state fits on 40×25 display without scrolling
- Navigation via single keypress (no complex menu trees)
- Always return to main menu option available

---

## State Transitions

```
┌─────────────────────┐
│   Hauptmenu         │
└──────┬──────────────┘
       ├─→ [1] Eintrag verwalten
       │    └─→ Suchmaske
       │         ├─→ [Enter] Nachname
       │         └─→ Trefferliste (Ergebnisse)
       │              ├─→ [1-N] Treffer waehlen
       │              │    └─→ Erfassungsmaske (Bearbeiten)
       │              │         ├─→ [Speichern] → Meldung → Hauptmenu
       │              │         ├─→ [Kopieren] → Meldung → Hauptmenu
       │              │         ├─→ [Loeschen] → Bestaetigung
       │              │         │                  ├─→ [J] → Meldung → Hauptmenu
       │              │         │                  └─→ [N] → Trefferliste
       │              │         └─→ [Abbrechen] → Trefferliste
       │              └─→ [Abbrechen] → Hauptmenu
       │
      ├─→ [2] Gesamtliste anzeigen
       │    └─→ Gesamtliste (paginated)
       │         ├─→ [Weiter] / [Zurück] → weitere Seiten
       │         └─→ [Hauptmenu] → Hauptmenu
       │
       └─→ [Q/Esc] Beenden
            └─→ Programm beendet
```

---

## Validation Criteria

| Kriterium                 | Beschreibung                                                    |
| ------------------------- | --------------------------------------------------------------- |
| Record Integrität         | Pipe-getrennte Struktur bleibt konsistent; keine Beschädigungen |
| Feldüberprüfung           | Keine Pipe- oder Newline-Zeichen in Feldwerten                  |
| Max. Recordlänge          | Kein Record > 255 Zeichen                                       |
| Trefferlisten-Paginierung | 5–8 Treffer pro Seite, Seitennav verfügbar                      |
| Fehlerbehandlung          | Disk-Fehler abgefangen; alte Daten unverändert                  |
| Bildschirm-Fits           | Alle States passen auf 40×25 ohne Scroll                        |

---

## BASIC Implementation Hints

### String Variables to Reserve

```basic
REM Adressdatensatz (bis 255 Zeichen)
DIM A$(200)              ' Aktueller Eintrag als pipe-separated String
DIM RS$(200)             ' Lese-Puffer fuer Records

REM Suchpuffer
DIM SN$(30)              ' Suchname (Nachname)
DIM TR$(200,10)          ' Trefferliste (bis 10 Treffer)
DIM TC%                  ' Treffercount

REM Bildschirm- und Navigations-State
REM SC$ = Bildschirmzustand ("MAIN", "SEARCH", "RESULTS", "FORM", "LIST", "CONFIRM", "MSG")
```

### I/O Handler

```basic
REM Error handling
ONERR GOTO ERROR_HANDLER
...
ERROR_HANDLER:
  IF ERR THEN PRINT "ERROR #";ERR; " - RETRY?"
  RESUME OR END
```

### Record Parsing & Formatting

```basic
REM Parse pipe-separated record into fields
REM Nutze MID$, INSTR für String-Manipulation

REM Example: Nachname = MID$(A$, 1, INSTR(A$, "|") - 1)
```

---

## Known Limitations & Workarounds

| Limitation             | Workaround                                                            |
| ---------------------- | --------------------------------------------------------------------- |
| Keine Built-in Sorting | Lese-Reihenfolge = Speicher-Reihenfolge (akzeptabel für <200 Records) |
| Keine Native DB        | REL-Datei + sequenzielle I/O + RAM-Buffer                             |
| 255-Char String Limit  | Passt: alle Records < 255 Char; kein Multi-String nötig               |
| Keine Transactions     | Fehler-Handling: Alte Daten im RAM bewahren; Neuversuch oder Abbruch  |
