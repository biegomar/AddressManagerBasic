# Research: C64 Address Manager Implementation

**Created**: 2026-04-26\
**Prepared for**: Implementation Planning Phase\
**Context**: Commodity 64 BASIC V2, VS64 toolchain, 1541 floppy storage

## Research Findings

### 1. C64 BASIC String Handling & 255-Character Limit

**Finding**: BASIC strings in C64 are limited to 255 characters by design of the
KERNAL ROM. Strings longer than 255 characters cannot be created or manipulated
as single entities.

**Implication for this feature**:

- Adressdatensätze à ~190 Zeichen passen sicher in einen 255er-String (mit
  Pipe-Trennzeichen und Reserve)
- Multi-String-Datensätze nicht erforderlich (Gut für Speicher)
- Feldlängen wurden entsprechend dimensioniert: max. 6 Felder × 30-40 Zeichen

**Validation**: Auf Emulator und echter Hardware (1541 Disk) testen mit
max-Feldgrößen.

### 2. 1541 Floppy & Random-Access File I/O

**Finding**: C64 BASIC kann über REL-Dateiformat (relative/random-access files)
direkten Zugriff auf Datenträgerpositionen ermöglichen:

- `OPEN 1, 8, 1, "ADDRESSES,L"` eröffnet eine REL-Datei im Lese-Modus
- `INPUT# / GET#` sequenziell oder nach SEEK
- SEEK-Befehle navigieren zu spezifischen Records

**Implication for this feature**:

- Pipe-getrennte Datensätze sollten in fester oder vorhersagbarer Struktur
  abgelegt werden
- Lese- und Schreibvorgänge müssen Fehler abfangen (IO-Fehler-Handler mit ONERR)
- Jeder Datensatz sollte eindeutig lokalisierbar sein (z.B. via Dateiposition
  oder Zeilennummer)

**Validation**: REL-Datei mit Test-Datensätzen auf 1541 anlegen,
Lese-/Schreib-Zyklen durchlaufen.

### 3. Screen I/O & 40x25 Display Management

**Finding**: C64 hat ein 40×25 Zeichengitter. Text-Eingabe und Ausgabe erfolgen
über:

- Direktes POKE in Video-RAM ($0400–$07FF für Zeichen, $D800–$DBFF für Farben)
- PRINT-Befehle mit CHR$(19) (CLRSCR), CHR$(18) (Reverse Video),
  Cursor-Positioning
- POKE 646,c für Textfarbe; POKE 53280,c für Border
- INPUT wartet auf Benutzereingabe, aber nur Zeichen-für-Zeichen auf der
  Eingabezeile

**Implication for this feature**:

- Bildschirmzustände können effizient mit PRINT-Kommandos und Cursor-Positioning
  gestaltet werden
- 40 Zeichen pro Zeile → Trefferlisten mit max. ~30–35 Zeichen pro Eintrag (+
  Nummer + Separator)
- 5–8 Treffer passen realistisch auf den Bildschirm (25 Zeilen – 2 Header – 2
  Footer = ~21 verfügbare Zeilen)

**Validation**: Screen-Layout für alle 7 Bildschirmzustände (Hauptmenü,
Suchmaske, Trefferliste, Erfassungsmaske, Gesamtliste, Bestätigung, Fehler) auf
Emulator testen.

### 4. VS64 Build Toolchain & Artifact

**Finding**: Das Projekt nutzt VS64 (Visual Studio für Commodore 64 BASIC). Der
Build-Output ist ein `.prg`-Dateiformat, das direkt in einem C64-Emulator oder
auf echtem Hardware geladen werden kann.

- `build/AddressManagerBasic.prg` ist die ausführbare Datei
- Build-Task: `vs64: build project` ist vorkonfiguriert

**Implication for this feature**:

- Alle Features müssen in `src/main.bas` implementiert werden (einzelne Datei)
- Nach Änderungen: Build-Befehl ausführen → .prg erzeugen → Validierung im
  Emulator
- Speicherung erfolgt auf der Emulator-Diskette oder echter 1541

**Validation**: Build-Prozess funktionsfähig; .prg-Datei ist ladbar.

### 5. Error Handling in C64 BASIC

**Finding**: C64 BASIC bietet begrenzte native Fehlerbehandlung:

- `ONERR GOTO LABEL` – globaler Error-Handler
- ERR-Variable enthält Fehlernummer
- Typisch: ERR=59 (I/O-Fehler), ERR=62 (VERIFY ERROR), etc.
- Nach Fehler: Manual recovery, kein automatisches Retry im Standard-BASIC

**Implication for this feature**:

- Error-Handler mit ONERR zur Abfang von Disk-Fehlern
- Benutzer-Rückmeldung mit Fehlermeldung (kein Retry)
- Alte Daten müssen vor kritischen Operationen intern gepuffert werden

**Validation**: I/O-Fehler-Szenarien (kein Laufwerk, Datei nicht vorhanden,
Lese-/Schreibfehler) manuell auslösen und Handler prüfen.

### 6. Memory Layout & Budget

**Finding**: C64 hat 64 KB RAM insgesamt. BASIC-Programme nutzen üblicherweise:

- $0000–$00FF: Zero Page (Systemvariablen, üblicherweise geschützt)
- $0100–$01FF: Stack
- $0200–$03FF: Input buffer, etc.
- $0400–$07FF: Video RAM (Zeichen, 1000 Bytes)
- $0800–$9FFF: BASIC-Programmbereich (~39 KB)
- $A000–$BFFF: KERNAL-ROM (nicht verfügbar bei Programmlauf)
- $C000–$CFFF: I/O & Systemregisters
- $D000–$DFFF: VIC/SID (Grafik, Sound – üblicherweise nicht für Daten nutzbar)
- $E000–$FFFF: KERNAL/ROM

**Implication for this feature**:

- ~39 KB für BASIC-Code und Variablen verfügbar
- Adressdatensätze (190 Zeichen × 200 = ~38 KB) KÖNNEN nicht alle im RAM
  gehalten werden
- Lösung: Nur aktuelle Datensätze und kleine Puffer im RAM; Rest auf Disk
- String-Variablen sollten sparsam genutzt werden

**Validation**: Speicherverbrauch mit Emulator-Tools überwachen; sicherstellen,
dass Datensätze Disk-basiert sind.

## Unresolved Clarifications Addressed

All five clarifications from the spec are now understood and integrated:

- ✅ Q1: Sequenzielle Nummerierung für Trefferlisten → Standard TEXT-UI Pattern
- ✅ Q2: ~200 Datensätze, 5–8 Treffer pro Seite → Speicher- und
  Bildschirm-realistisch
- ✅ Q3: 6 Felder à spezifische Länge → Passt in 255er-String
- ✅ Q4: Fehlerbehandlung einfach (kein Retry) → ONERR-Handler ausreichend
- ✅ Q5: Exakte Suche, Speicherreihenfolge → Keine Komplexität durch
  Sortierung/Wildcard

## Next Steps

→ Phase 1: Data Model und Technical Design basierend auf diesen Findings
erstellen
