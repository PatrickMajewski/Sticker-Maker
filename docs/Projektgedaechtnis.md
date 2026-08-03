# Projektgedächtnis

Diese Datei liegt im Arbeitsprojekt als `CLAUDE.md` im Stammverzeichnis und wird dort bei jeder
Unterhaltung automatisch gelesen. Hier im Repository ist sie umbenannt, damit sie nicht mit dem
Projektgedächtnis eines anderen Projekts kollidiert. Wer den Sticker Maker weiterentwickelt,
legt sie unter dem Namen `CLAUDE.md` in den Projektstamm.

---

# Sticker Maker — Projektgedächtnis

Dieses Projekt ist ein **Sticker-Maker als Browser-App**. UI-Sprache Deutsch mit EN-Umschalter.
Kunde ist kein Entwickler — Antworten in Deutsch, ohne Framework-Jargon, und Lösungen müssen
ohne Build-Schritt, Konto oder Store-Account laufen.

**Vollständiges Protokoll mit Quellen, Entscheidungen und Grenzen: `HANDOFF.md`. Bei Übernahme
in ein anderes Projekt zuerst dort lesen.**

## Dateien

| Datei | Rolle |
| --- | --- |
| `Sticker Maker.dc.html` | Arbeitsversion — hier wird entwickelt |
| `Sticker Maker v1.2 (Final Release).dc.html` | **Aktueller Final Release, vom Nutzer freigegeben (03.08.2026).** Rollback-Ziel. Nicht ohne Auftrag ändern |
| `Sticker Maker v1.2 (Backup).dc.html` | identische Sicherung für Experimente (z. B. weitere KI-Modelle) |
| `Sticker Maker v1.2 (standalone).html` | Einzeldatei-Export zum Weitergeben/Hosten (aus der Arbeitsversion neu bauen, wenn sich was ändert) |
| `Sticker Maker v1.1/v2/v1 (Backup).dc.html` | ältere Stände — v1.1 ist der Release vor dem Dunkelmodus, v2 enthält noch den Modell-Umschalter als Vorlage |
| `fonts.css` | die zehn Textschriften als eingebettete `data:`-WOFF2 — Text läuft offline |
| `uploads/Spotify Playlist Designer App/` | Referenz-App des Nutzers — Designvorlage, siehe deren `README.md` |
| `_ds/modernist-0d821174-.../` | gebundenes Design-System (Modernist) |

**Status: abgeschlossen und eingefroren.** Der Nutzer arbeitet mit v1.2 weiter; die nächste
geplante Etappe ist eine echte mobile App (siehe `HANDOFF.md`, Abschnitt 7). Weitere KI-Modelle
nachrüsten: `HANDOFF.md`, Abschnitt 6a. Ein bewusst aufgeschobener Leistungsbefund steht in 6b —
nicht ungefragt umsetzen. Bis dahin bitte nichts unaufgefordert umbauen.

## Aufbau in einem Satz

Alles hängt an **einer Graustufen-Maske** (`this.maskC`, weiß = Sticker). Woher die kommt —
KI-Erkennung, Offline-Chroma-Fallback, Pinsel, Pfad-Polygon oder Rechteck mit runden Ecken — ist
der Pipeline egal. Rand, Used Look, Holo und Export lesen nur die Maske. **Neue Formquellen
liefern eine Maske, sie fassen die Pipeline nicht an.**

Drei Modi in Sektion 01: `mode` = `full` (Zuschnitt → Formwahl eckig/gerundet), `path`
(Punkte klicken), `ai` (Modell), `cut` (PNG-Alpha unverändert übernehmen).
`step` = `crop` | `path` | `edit`. Der Modus-Umschalter liegt **außerhalb** von
`data-dim="hasImage"`, damit er vor dem Bildladen bedienbar ist.
Die **Erkennung ist eine Registry** (`MODELS`) — weitere Modelle nachrüsten: `HANDOFF.md` 6a.

**Zoom** (`state.zoom`, 1 = eingepasst) wird als CSS-Breite des Canvas angewendet — nicht als
Canvas-Transform — damit `ptToCanvas()` und damit alle Werkzeuge unverändert treffen.
Pfadpunkte sind absolute Koordinaten der Arbeitsauflösung und werden bei Bildwechsel,
Zuschnitt und Moduswechsel geleert.

## Verbindliche Konventionen

- **Design Component** (`dc_write` / `dc_html_str_replace` / `dc_js_str_replace`), keine `write_file` auf `.dc.html`.
- **Design-System Modernist** ist bindend, aber mit den **bewussten Radius-Abweichungen** der
  Referenz-App: Karten 12px, Block-Buttons & Segmented 10px, Icon-Buttons/Thumbnails 8px,
  Swatches 5px, Tags/Pills 999px. Sektionsnummern `01`–`05` in `--color-accent-700`.
  Trennlinien im Panel sind `--color-accent` (Nutzerwunsch), die Regel unter dem Header bleibt neutral.
- **Kein `{{ }}`-Hole für statischen Text, Stil oder Slider-Werte.** Statt dessen die
  imperative Synchronisierung in `syncUI()` über `data-*`-Attribute:
  `data-k` (Slider↔State), `data-out`+`data-fmt` (Wertanzeige), `data-seg`+`data-val`
  (Segmented-Buttons), `data-show`/`data-hide`+`data-disp` (Sichtbarkeit),
  `data-dim` (45 % disabled, **nicht kumulativ**), `data-tag`, `data-bar`, `data-thumb`,
  `data-en` (englische Übersetzung, `applyLang()` tauscht `textContent`).
  Grund: die Oberfläche malt sofort beim Streamen und bleibt im Editor bearbeitbar.
- **Zweisprachigkeit:** deutscher Text steht statisch im Template, englischer in `data-en`.
  Platzhalter über `data-en-placeholder` (eigener Durchgang — `textContent` erreicht keine
  Attribute). Statusmeldungen als Paar `stDe`/`stEn` über `this.say(de, en)`.
- **Dunkelmodus** ist allein die `DARK`-Variablen-Map plus `applyTheme()`. Wer sie anfasst:
  **vier Fallen in `HANDOFF.md` 2a lesen** (beide Rampenenden spiegeln, `#f8f4f4` auf jeder
  Akzentfüllung, `color-scheme`, feste Vorschau-Gründe).
- **Besitzer-Wächter:** `owns()` steht am Anfang von `syncUI`, `applyLang`, `applyTheme`,
  `applyView` und `draw`. Neue Methoden, die ins DOM schreiben, brauchen ihn auch.
- **Caches** `_a _cut _sil _used _wash _holo _rim _text` werden über `invalidate(level)`
  verworfen (`alpha` → alles, `border`, `used`, `holo`, `text`). Slider tragen `data-lv` mit ihrem Level.
  Wer eine neue Ableitung baut, muss sie dort eintragen.
