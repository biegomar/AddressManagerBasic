## Allgemein

Ich möchte im C64-Basic einen Adressen-Manger erstellen, der ein paar einfache
Funktionen beherrscht. Nach dem Programmstart kommt man in ein visuelles
Hauptmenü. Hier gibt es drei Funktionen und den Punkt "Beenden". Die drei
Funktionen sind:

1. einen Adress-Eintrag verwalten
2. eine Liste aller Adressen ausgeben

Die Menüauswahl erfolgt über eine Tasteneingabe.

## Einträge verwalten

### Eintrag suchen

In einem neuen Bildschirm kann man über den Nachnamen nach einer Person suchen.
In diesem Bildschirm werden dann alle Datensätze angezeigt, die gefunden wurden.
Man kann dann einen Datensatz auswählen und dieser wird als aktueller Datensatz
im Speicher gehalten. Nun kann ich in weitere Funktionen verzweigen. Editieren.
Löschen.

### Eintrag editieren

Wählt man Editieren, so gelangt man in die Erfassungsmaske und kann nun sogar
Felder verändern und den Datensatz wieder speichern. Wenn man möchte kann man
den Datensatz sogar als Kopie anlegen.

### Eintrag löschen

Wählt man löschen, so wird noch einmal nachgefragt und der Datensatz vom
Datenträger und aus der Liste entfernt.

### Eintrag anlegen

Um einen neuen Eintrag anzulegen, wechselt das Programm in einen neuen
Bildschirm und man hat die Möglichkeit ein paar Standardfelder zu erfassen und
den Eintrag zu speichern.

## Datenspeicherung

Alle Felder sollen durch | getrennt in nur einem String abgelegt werden.
Vorsicht! Ein String kann nur 255 Zeichen speichern. Falls das nicht reicht,
muss es zwei oder mehrere Strings geben. Dann aber logisch zusammenhängend. Also
z.B. ein String die Adresse, ein weiterer die Daten der Person. Generell sollen
die Adressen in einer Datei mit wahlfreiem Zugriff gespeichert werden.
