# Feature Specification: C64 Address Manager

**Feature Branch**: `[002-address-manager-basic]`\
**Created**: 2026-04-26\
**Status**: Draft\
**Input**: User description: "C64-BASIC Adressmanager mit Hauptmenue,
Such-/Bearbeitungsfluss, Gesamtliste und Dateispeicherung mit pipe-getrennten
Datensaetzen"

## User Scenarios & Validation _(mandatory)_

### User Story 1 - Neue Adresse erfassen (Priority: P1)

Als Anwender moechte ich aus dem Hauptmenue eine neue Adresse erfassen und
speichern, damit neue Kontakte ohne Umweg in den Datenbestand aufgenommen werden
koennen.

**Why this priority**: Ohne das Anlegen neuer Datensaetze bleibt der
Datenbestand statisch und der Nutzen des Programms ist stark eingeschraenkt.

**Independent Validation**: Im Emulator oder auf Hardware das Programm starten,
im Hauptmenue den Erfassungsweg waehlen, alle Standardfelder fuellen, speichern
und den neuen Datensatz anschliessend ueber Suche oder Gesamtliste wiederfinden.

**Acceptance Scenarios**:

1. **Given** das Hauptmenue ist sichtbar, **When** der Anwender die Funktion zum
   Anlegen eines neuen Eintrags waehlt, **Then** erscheint eine Erfassungsmaske
   mit den vorgesehenen Standardfeldern.
2. **Given** die Erfassungsmaske ist ausgefuellt, **When** der Anwender
   speichert, **Then** wird der Datensatz dauerhaft abgelegt und kann in einer
   spaeteren Programmsitzung wieder gefunden werden.
3. **Given** ein Feld oder der gesamte Datensatz ueberschreitet die verfuegbare
   String-Kapazitaet, **When** gespeichert werden soll, **Then** wird der
   Datensatz regelkonform auf logisch zusammenhaengende Teile verteilt oder mit
   einer verstaendlichen Rueckmeldung abgewiesen.

---

### User Story 2 - Vorhandene Adresse suchen und verwalten (Priority: P2)

Als Anwender moechte ich nach dem Nachnamen suchen, einen gefundenen Datensatz
als aktuellen Eintrag waehlen und ihn danach bearbeiten, kopieren oder loeschen,
damit vorhandene Kontakte gepflegt werden koennen.

**Why this priority**: Die Verwaltung bestehender Daten ist der zweite
Kernnutzen des Programms und haengt logisch von vorhandenen Eintraegen ab.

**Independent Validation**: Im Emulator oder auf Hardware mindestens zwei
Datensaetze mit gleichem Nachnamen speichern, nach diesem Nachnamen suchen,
einen Treffer auswaehlen, den Eintrag bearbeiten, als Kopie speichern und danach
einen Eintrag mit Sicherheitsabfrage loeschen.

**Acceptance Scenarios**:

1. **Given** gespeicherte Adressen mit passendem Nachnamen existieren, **When**
   der Anwender im Suchbildschirm einen Nachnamen eingibt, **Then** werden alle
   passenden Datensaetze im Ergebnisbildschirm angezeigt.
2. **Given** eine Trefferliste ist sichtbar, **When** der Anwender einen Treffer
   auswaehlt, **Then** wird dieser Datensatz als aktueller Eintrag im Speicher
   gehalten und die Folgefunktionen Bearbeiten, Kopieren und Loeschen werden
   verfuegbar.
3. **Given** ein aktueller Eintrag ist gewaehlt, **When** der Anwender
   Bearbeiten oder Kopieren ausfuehrt und speichert, **Then** bleiben die
   geaenderten oder duplizierten Daten dauerhaft erhalten.
4. **Given** ein aktueller Eintrag ist gewaehlt, **When** der Anwender Loeschen
   waehlt und die Sicherheitsabfrage bestaetigt, **Then** wird der Datensatz aus
   Speicherung und Ergebnisliste entfernt.
5. **Given** ein aktueller Eintrag ist gewaehlt, **When** der Anwender die
   Loeschabfrage abbricht, **Then** bleibt der Datensatz unveraendert erhalten.

---

### User Story 3 - Gesamtliste anzeigen (Priority: P3)

Als Anwender moechte ich eine Liste aller gespeicherten Adressen sehen, damit
ich den gesamten Datenbestand schnell ueberblicken kann.

**Why this priority**: Die Gesamtliste ist wertvoll fuer Orientierung und
Kontrolle, ist aber nachrangig gegenueber Anlegen und gezielter Pflege einzelner
Datensaetze.

**Independent Validation**: Im Emulator oder auf Hardware mehrere Datensaetze
anlegen, im Hauptmenue die Listenfunktion waehlen und pruefen, dass alle
gespeicherten Adressen in einer lesbaren, seitenweisen oder schrittweisen
Ausgabe erscheinen.

**Acceptance Scenarios**:

1. **Given** gespeicherte Adressen existieren, **When** der Anwender die
   Listenfunktion waehlt, **Then** zeigt das Programm eine vollstaendige und
   lesbare Ausgabe aller Datensaetze.
2. **Given** mehr Datensaetze vorhanden sind, als auf einen Bildschirm passen,
   **When** die Liste angezeigt wird, **Then** bleibt die Navigation fuer den
   Anwender nachvollziehbar und kein Datensatz geht in der Anzeige verloren.

### Edge Cases

- Es werden keine Treffer fuer einen gesuchten Nachnamen gefunden.
- Mehr Treffer oder Listeneintraege sind vorhanden, als auf einen
  40x25-Bildschirm passen.
- Ein einzelnes Feld oder ein gesamter Datensatz stoesst an die
  255-Zeichen-Grenze eines BASIC-Strings.
- Das Speichermedium ist nicht verfuegbar, liefert einen Lesefehler oder bricht
  waehrend eines Schreibvorgangs ab.
- Der Anwender verlaesst eine Erfassungs- oder Bearbeitungsmaske ohne Speichern.
- Nach einem fehlgeschlagenen Loesch- oder Speichervorgang muss der vorherige
  Datenbestand unveraendert bleiben.

## Platform Constraints _(mandatory)_

- **PC-001**: Das Feature umfasst klar getrennte Bildschirmzustaende fuer
  Hauptmenue, Suchmaske, Trefferliste, Erfassungs-/Bearbeitungsmaske,
  Gesamtliste, Loeschbestaetigung und Rueckmeldungen nach Speichern oder
  Fehlern. Die Navigation erfolgt ausschliesslich ueber Tastatureingaben mit
  sichtbaren Auswahlhinweisen.
- **PC-002**: Das Feature arbeitet mit einem knappen Speicherbudget.
  Gleichzeitig im Arbeitsspeicher gehalten werden duerfen nur die aktuell
  benoetigten Bildschirmdaten, der aktuelle Datensatz und eine begrenzte
  Treffermenge. Datensaetze muessen bei Bedarf in logisch zusammenhaengende
  String-Teile aufgeteilt werden, sobald eine Einzelzeichenkette die
  255-Zeichen-Grenze nicht mehr sicher einhaelt.
- **PC-003**: Die Speicherung erfolgt als pipe-getrennte Datensaetze in einer
  Datei mit wahlfreiem Zugriff. Falls ein Datensatz auf mehrere String-Teile
  verteilt werden muss, bleiben diese Teile logisch gekoppelt und gemeinsam les-
  und schreibbar.
- **PC-004**: Die manuelle Validierung muss auf Emulator oder echter Hardware
  mindestens den Weg Programmstart -> neuer Eintrag -> Suche -> Bearbeiten oder
  Kopieren -> Liste -> Loeschen -> Programmende abdecken, einschliesslich
  mindestens eines Diskettenfehlers oder Abbruchs.

## Requirements _(mandatory)_

### Functional Requirements

- **FR-001**: Das System MUSS nach dem Programmstart ein visuelles Hauptmenue
  mit den Punkten Eintrag verwalten, Gesamtliste anzeigen und Beenden anzeigen.
- **FR-002**: Das System MUSS die Menueauswahl ueber einzelne Tastatureingaben
  annehmen und dem Anwender jederzeit sichtbar machen, welche Auswahl moeglich
  ist.
- **FR-003**: Das System MUSS das Anlegen eines neuen Adressdatensatzes ueber
  eine Erfassungsmaske mit Standardfeldern ermoeglichen.
- **FR-004**: Das System MUSS neue und geaenderte Datensaetze dauerhaft
  speichern, so dass sie nach einem Neustart des Programms wieder verfuegbar
  sind.
- **FR-005**: Das System MUSS die Suche nach Adressen ueber den Nachnamen
  ermoeglichen.
- **FR-006**: Das System MUSS nach einer Suche alle passenden Treffer anzeigen
  und dem Anwender die Auswahl eines aktuellen Datensatzes erlauben.
- **FR-007**: Das System MUSS den ausgewaehlten Datensatz waehrend des
  Verwaltungsvorgangs als aktuellen Eintrag im Speicher halten.
- **FR-008**: Das System MUSS fuer den aktuellen Eintrag die Funktionen
  Bearbeiten, Als Kopie speichern und Loeschen bereitstellen.
- **FR-009**: Das System MUSS vor dem Loeschen eines Datensatzes eine
  ausdrueckliche Bestaetigung verlangen.
- **FR-010**: Das System MUSS eine vollstaendige Liste aller gespeicherten
  Adressen anzeigen koennen.
- **FR-011**: Das System MUSS Datensaetze in einer pipe-getrennten Struktur
  speichern.
- **FR-012**: Das System MUSS verhindern, dass die 255-Zeichen-Grenze eines
  einzelnen BASIC-Strings zu stiller Datenbeschaedigung fuehrt.
- **FR-013**: Das System MUSS bei Lese-, Schreib- oder Datentraegerfehlern eine
  verstaendliche Rueckmeldung geben und den bis dahin gueltigen Datenbestand
  schuetzen.
- **FR-014**: Das System MUSS Abbrueche in Eingabe-, Bearbeitungs- und
  Loeschdialogen so behandeln, dass ohne bestaetigtes Speichern oder Loeschen
  keine unbeabsichtigte Aenderung entsteht.

### Key Entities _(include if feature involves data)_

- **Adressdatensatz**: Ein gespeicherter Kontakt mit Standardfeldern wie
  Nachname, Vorname, Strasse, Ort und Telefonnummer sowie der pipe-getrennten
  Darstellung fuer die Ablage.
- **Aktueller Eintrag**: Der vom Anwender aus einer Trefferliste ausgewaehlte
  Datensatz, auf den sich Bearbeiten, Kopieren und Loeschen beziehen.
- **Trefferliste**: Die geordnete Menge aller Datensaetze, die auf einen
  eingegebenen Nachnamen passen und aus der ein aktueller Eintrag gewaehlt
  werden kann.
- **Datensatzsegment**: Ein logisch zugehoeriger Teil eines Adressdatensatzes,
  der genutzt wird, wenn der gesamte Inhalt nicht sicher in einen einzelnen
  BASIC-String passt.

## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: Ein Anwender kann nach dem Programmstart innerhalb von 30 Sekunden
  die gewuenschte Hauptmenuefunktion erkennen und aufrufen.
- **SC-002**: Ein neuer Adressdatensatz mit den vorgesehenen Standardfeldern
  kann von einem geuebten Anwender in unter 2 Minuten erfasst und gespeichert
  werden.
- **SC-003**: Eine Suche nach einem vorhandenen Nachnamen fuehrt unter normalen
  Diskettenbedingungen in spaetestens 5 Sekunden zu einer sichtbaren
  Trefferanzeige.
- **SC-004**: In der manuellen Validierung koennen die Fluesse Anlegen, Suchen,
  Bearbeiten oder Kopieren, Gesamtliste und Loeschen in einer Sitzung ohne
  Neustart erfolgreich abgeschlossen werden.
- **SC-005**: Bei Abbruch einer Loeschung oder bei einem simulierten
  Schreibfehler bleibt der zuvor gespeicherte Datensatzbestand unveraendert
  erhalten.

## Assumptions

- Standardfelder fuer einen Eintrag sind mindestens Nachname, Vorname, Strasse,
  Ort und Telefonnummer.
- Das Hauptmenue stellt zwei Fachfunktionen und den Punkt Beenden bereit; die
  Formulierung mit drei Funktionen in den Ausgangsnotizen wird als redaktionelle
  Ungenauigkeit behandelt.
- Die Tastatur ist das einzige Eingabegeraet fuer die erste Version;
  Joystick-Bedienung ist nicht Bestandteil des Umfangs.
- Die Gesamtliste und Trefferlisten duerfen seitenweise oder schrittweise
  dargestellt werden, solange der Anwender nachvollziehbar weiterblaettern kann.
- Der bestehende Datenbestand darf kuenftig wachsen, bleibt aber in einem
  Umfang, der mit sequentiellem Anwenderfluss und begrenzter Trefferanzeige
  handhabbar ist.
