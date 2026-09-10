# Termine – Finkenau Musikkindergarten

Eine einzelne statische Webseite mit den Kita-Terminen, gehostet über GitHub
Pages: **https://steffen0tt.github.io/Termine/**

## Aufbau

| Datei | Zweck |
|---|---|
| `index.html` | Layout, Design und die gesamte Anzeige-Logik (JavaScript) |
| `events.json` | **Nur hier stehen die Termine** – diese Datei bearbeitest du, um Termine hinzuzufügen, zu ändern oder zu löschen |

Vorher standen die Termine fest im JavaScript-Code der `index.html`. Das ist
jetzt getrennt: `index.html` musst du im Normalfall **nie mehr anfassen**.

## Termine bearbeiten

Am einfachsten direkt auf github.com, ganz ohne Git-Kenntnisse:

1. Auf `events.json` im Repo klicken.
2. Auf das Stift-Symbol ("Edit this file") oben rechts klicken.
3. Termin hinzufügen/ändern/löschen (siehe Format unten).
4. Unten "Commit changes" klicken – die Live-Seite aktualisiert sich
   automatisch nach ein bis zwei Minuten.

Alternativ lokal: `events.json` in einem Texteditor öffnen, ändern,
speichern, `git add events.json`, `git commit -m "Termine aktualisiert"`,
`git push`.

### Format eines Termins

```json
{
  "title": "Elternabend Flötenfohlen",
  "start": "2026-02-04T18:00:00",
  "end":   "2026-02-04T19:00:00"
}
```

- **Ganztägig / mehrtägig** (z. B. "Kita geschlossen"): Datum ohne Uhrzeit,
  z. B. `"start": "2026-07-20", "end": "2026-08-01"`.
- **Mit Uhrzeit** (z. B. Elternabend): `"YYYY-MM-DDTHH:MM:SS"`.
- **Gruppe**: wird automatisch erkannt, wenn der Gruppenname
  (Klavierschildkröten, Klangkobolde, Trompetentiger, Flötenfohlen,
  Melodiezauberer, Musikwichtel) irgendwo im `title` vorkommt. Kein
  Gruppenname im Titel → Termin gilt für alle ("Alle"-Filter).
- **Typ/Farbe** (Elternabend, Kita geschlossen, Fest, Elternrat/Info,
  Sonstiges) wird ebenfalls automatisch an Schlüsselwörtern im Titel
  erkannt. Falls das mal danebenliegt, kannst du optional ein Feld
  `"type"` setzen: `parents`, `closed`, `party`, `info` oder `other`.

### Neuen Termin hinzufügen

Ein neues `{ ... }`-Objekt in die Liste einfügen – **auf das Komma vor der
neuen Zeile achten**:

```json
  {
    "title": "Elternrat",
    "start": "2026-05-20T18:00:00",
    "end": "2026-05-20T19:00:00"
  },
  {
    "title": "Dein neuer Termin",
    "start": "2026-06-01T18:00:00",
    "end": "2026-06-01T19:00:00"
  }
```

### Termin löschen

Das komplette `{ ... }`-Objekt (inkl. eines der beiden umgebenden Kommas)
entfernen.

### Typische Fehlerquellen

- Vergessenes oder überzähliges Komma zwischen den Termin-Objekten.
- Datum ohne Anführungszeichen oder im falschen Format.
- Fehlende schließende `}` oder `]`.

Falls `events.json` ungültig ist, zeigt die Seite jetzt eine Fehlermeldung
an, statt einfach leer zu bleiben (siehe "Was wurde geändert" unten). Zur
Kontrolle kann der Inhalt z. B. auf https://jsonlint.com eingefügt werden.

## Lokal testen (optional)

`index.html` per Doppelklick öffnen funktioniert **nicht mehr**, weil
Browser das Nachladen einer lokalen `events.json` per `file://` aus
Sicherheitsgründen blockieren. Stattdessen im Ordner einen kleinen lokalen
Server starten:

```
python3 -m http.server 8000
```

und dann `http://localhost:8000` im Browser öffnen.

## Was wurde geändert (Sep 2026)

**iCal-Bug + Datenstruktur:**
- **Bug behoben**: Der Link "Alle Termine als iCal herunterladen" im Footer
  war tot (auf `#`, der Code dafür war auskommentiert). Er lädt jetzt
  tatsächlich eine `.ics`-Datei mit allen Terminen herunter.
- **Termine ausgelagert**: von hartcodiertem JavaScript in `index.html` nach
  `events.json`, damit Termine ohne Code-Kenntnisse gepflegt werden können.
- **Fehlerbehandlung ergänzt**: Ungültige Termine oder eine fehlerhafte
  `events.json` führen jetzt zu einer sichtbaren Warnung/Fehlermeldung statt
  zu einer leeren, stillen Seite.
- **Kleinere Code-Aufräumarbeiten**: doppelter Code bei der Datumsformatierung
  reduziert, `detectType`/`createVEvent` vereinheitlicht, IDs der iCal-Events
  deterministischer statt zufällig.

**Navigation & Design (2. Runde):**
- **Direkt zu aktuellen Terminen**: Die Seite berechnet beim Laden anhand des
  heutigen Datums, was schon vorbei ist. Vergangene Termine werden nicht mehr
  oben angezeigt, sondern in einem eingeklappten Bereich "Vergangene Termine
  (N) anzeigen" ganz unten (dort leicht ausgegraut) – man landet also direkt
  bei den kommenden Terminen, ohne durch Monate alte Einträge zu scrollen.
- **"Nächste Termine"-Leiste**: oben unter dem Header eine kompakte,
  horizontal scrollbare Übersicht der nächsten 4 anstehenden Termine mit
  Klick-Sprung zur vollständigen Karte weiter unten.
- **"Heute" / "Morgen" / "in N Tagen"-Hinweis**: Termine in den nächsten 7
  Tagen bekommen ein kleines blaues Badge, damit man auf einen Blick sieht,
  was bald ansteht.
- **Mobile Navigation verschlankt**: Die Gruppen-Filter scrollen auf
  schmalen Bildschirmen horizontal in einer Zeile statt in vier Zeilen
  umzubrechen; der Seitentitel bricht nicht mehr mitten im Wort um.
- **Kompaktere Karten**: Die drei Buttons (iCal/Google/Outlook) sind zu
  einem einzigen "🗓️ Kalender"-Dropdown zusammengefasst, das sich beim Klick
  öffnet – spart auf jeder Karte deutlich Platz.

## Ideen für später (nicht umgesetzt)

- **Noch einfachere Pflege**: Termine in einer Google-Tabelle pflegen statt
  in JSON, und die Seite lädt die veröffentlichte Tabelle als CSV. Dann
  reicht eine neue Zeile in Excel/Sheets – kein JSON-Format mehr nötig.
  Aufwand: einmalige Einrichtung der Tabelle + Freigabe "im Web
  veröffentlichen".
- **Eingabemaske**: eine winzige Zusatzseite mit einem Formular, das
  automatisch ein korrektes JSON-Objekt erzeugt (zum Copy-Paste), damit man
  sich um Kommas/Anführungszeichen gar nicht mehr kümmern muss.
- **Barrierefreiheit**: Farben der "Pills" sind zusätzlich mit Icon + Text
  gekennzeichnet (gut für Farbfehlsichtige), Kontrast könnte bei den
  Pastelltönen (z. B. `type-info`) noch etwas erhöht werden.
- **Datenstand im Footer**: "Stand: Oktober 2025" ist ein manuell gepflegter
  Text – leicht zu vergessen bei Änderungen. Könnte durch einen Hinweis auf
  das letzte `git`-Commit-Datum ersetzt oder ganz entfernt werden.
- **iCal-Sammel-Download prominenter platzieren**: liegt aktuell weiterhin
  im Footer; könnte stattdessen als sichtbarer Button im Header stehen, da
  er jetzt funktioniert und der praktischste Weg ist, alle Termine auf
  einmal zu abonnieren.
