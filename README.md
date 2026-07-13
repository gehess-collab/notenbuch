# Notenbuch

Ein digitales Notenbuch für Lehrkräfte als eine einzige, eigenständige HTML-Datei — kein Server, keine Installation, keine Cloud. Alle Notendaten bleiben ausschließlich auf dem Gerät der Nutzerin bzw. des Nutzers.

**App direkt öffnen:** [gehess-collab.github.io/notenbuch/notenbuch.html](https://gehess-collab.github.io/notenbuch/notenbuch.html)

## Was kann es

- **Zwei Notensysteme:** Note 1–6 (Sek I, mit Halbjahres- und Jahresnote) und Notenpunkte 0–15 (Kursstufe, je Halbjahr eine eigenständige Endnote).
- **Flexible Gewichtung:** frei definierbare Kategorien (Klassenarbeit, Test, Mündlich, GFS, …) mit prozentualer Gewichtung; Kategorien können optional in eine andere „falten“ (z. B. Tests zählen anteilig zu Klassenarbeiten).
- **Verschiedene Erfassungsarten je Leistung:** Punkte mit Teilaufgaben und automatisch erzeugtem Notenschlüssel (linear oder mit Knick), direkte Noteneingabe, Diktate (Fehleranzahl-Schlüssel) sowie laufend dokumentierte mündliche Tendenzen (++ / + / 0 / − / −−).
- **Datierte Notizen** je Schüler/in, z. B. für GFS-Themen oder Gesprächsnotizen — einzeln als „im Ausdruck zeigen“ markierbar.
- **Einzelauszug** pro Schüler/in zum Ausdrucken (Elterngespräch, Zeugniskonferenz), CSV-Export der Gesamtübersicht.
- **Passwortschutz:** optionale clientseitige Verschlüsselung (AES-GCM) der gespeicherten Datei.
- **Ein-Schritt-Rückgängig** als Sicherheitsnetz gegen versehentliches Löschen.

## Persistenz

Es gibt **keinen Server und kein `localStorage`** — alle Daten liegen in einer selbst gewählten `.json`-Datei:

- **Chrome/Edge (empfohlen):** direkter Dateizugriff über die File System Access API inklusive automatischem Speichern.
- **Andere Browser** (Firefox, iOS/iPadOS Safari): Öffnen per Dateiauswahl, Speichern über das native Teilen-Menü oder als Download — kein Autosave, jede Änderung braucht ein explizites „Speichern“.

Die hier gehostete Version ist nur die leere App-Hülle; es werden keine Daten an einen Server übertragen.

## Lokal nutzen

`notenbuch.html` einfach im Browser öffnen (Chrome oder Edge empfohlen). Ein Build-Schritt oder Abhängigkeiten sind nicht nötig — die Datei ist vollständig selbstständig (HTML, CSS und JavaScript in einer Datei).

## Anleitung

- [Anleitung zur App-Nutzung](https://gehess-collab.github.io/notenbuch/anleitung-notenbuch.html)

## Lizenz

[MIT](LICENSE) — freie Nutzung, Veränderung und Weitergabe, ohne Gewährleistung.
