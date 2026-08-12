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
| `Sticker Maker v1.4 (Final Release).dc.html` | **Aktueller Meilenstein, vom Nutzer freigegeben (12.08.2026).** Rollback-Ziel. Nicht ohne Auftrag ändern |
| `Sticker Maker v1.4 (Backup).dc.html` | identische Sicherung für Experimente (z. B. weitere KI-Modelle) |
| `Sticker Maker v1.4 (standalone).html` | Einzeldatei-Export zum Weitergeben/Hosten (aus der Arbeitsversion neu bauen, wenn sich was ändert) |
| `Sticker Maker v1.3.5 (Backup).dc.html` | vorheriger Meilenstein — letzter Stand ohne WebP |
| `Sticker Maker v1.3.1 (Backup).dc.html` | vorheriger Meilenstein — zweite mobile Runde |
| `Sticker Maker v1.3 (Backup).dc.html` | erste mobile Fassung — Vergleichspunkt für die zweite Runde |
| `Sticker Maker v1.2.3 (Backup).dc.html` | **letzter reiner Desktop-Stand** — der Vergleichspunkt, wenn eine mobile Änderung am Rechner etwas kaputt macht |
| `Sticker Maker v1.2.2/v1.2.1/v1.2/v1.1/v2/v1 (Backup).dc.html` | ältere Stände — v1.2.2 ist der Release vor Blockwahl und Folien-Optik in der Stanzung, v1.2.1 der vor Pinsel-Umbau und Undo, v1.2 der vor dem Distanzfeld, v1.1 der vor dem Dunkelmodus, v2 enthält noch den Modell-Umschalter als Vorlage |
| `release/` | fertiger GitHub-Ordner (App, `index.html` für Pages, Quelltext, Doku, LICENSE) |
| `fonts.css` | die zehn Textschriften als eingebettete `data:`-WOFF2 — Text läuft offline |
| `uploads/Spotify Playlist Designer App/` | Referenz-App des Nutzers — Designvorlage, siehe deren `README.md` |
| `_ds/modernist-0d821174-.../` | gebundenes Design-System (Modernist) |

**Status: mobile Fassung abgeschlossen, v1.4 als Meilenstein eingefroren.** Eine Datei bedient
Telefon (Hoch- und Querformat) und Rechner; Look and Feel sind unverändert geblieben. Was noch
offen ist: `HANDOFF.md`, Abschnitt 6. Weitere KI-Modelle nachrüsten: 6a. Ein bewusst
aufgeschobener Leistungsbefund steht in 6b — nicht ungefragt umsetzen. Der mobile Umbau selbst
ist in **Abschnitt 8** beschrieben, die Bedienlogik des Panels in **8a**; wer am Layout, an den
Gesten, an den Ecken-Kapseln oder an den Blocktiteln arbeitet, liest die zuerst. Ein offener
Befund zum **Abrieb** samt Umbauvorschlag steht in **5b** — der Gegenvorschlag des Nutzers steht
aus, also nicht ungefragt umsetzen.

## Aufbau in einem Satz

Alles hängt an **einer Graustufen-Maske** (`this.maskC`, weiß = Sticker). Woher die kommt —
KI-Erkennung, Offline-Chroma-Fallback, Pinsel, Pfad-Polygon oder Rechteck mit runden Ecken — ist
der Pipeline egal. Rand, Used Look, Holo und Export lesen nur die Maske. **Neue Formquellen
liefern eine Maske, sie fassen die Pipeline nicht an.**

Vier Modi in Sektion 01: `mode` = `full` (Zuschnitt → Formwahl eckig/gerundet), `path`
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
- **Caches** `_a _edt _edtBox _plate _bb _rimF _cut _sil _used _wash _holo _rim _text` werden über `invalidate(level)`
  verworfen (`alpha` → alles, `border`, `used`, `holo`, `text`). Slider tragen `data-lv` mit ihrem Level.
  Wer eine neue Ableitung baut, muss sie dort eintragen. **`_edt` hängt bewusst nur an `alpha`** —
  das Distanzfeld überlebt Randbreiten-Änderungen, deshalb ist es für `MAXBORDER` gepolstert und
  wird über einen Versatz gelesen. Wer am Rand arbeitet: **`HANDOFF.md` 3a lesen** (Feldgeometrie,
  Schwellen statt Verdichten, keine Breitenkompensation, gepolsterter Weichzeichner in `alphaC`).
  **`_rimF` ist die Ausnahme von der Regel:** es hängt nicht an einer Ebene, sondern an einem
  Schlüssel aus `borderW`/`smooth`/`borderOn` — die Schmutzregler dürfen es nicht neu bauen.
- **Kanten sind Schnitte, keine Verläufe.** Kein Nachblur in `silC`; Kantenweichheit ist bei
  erzeugten Formen 0 (`featherFor(mode)`), nur die Erkennung bekommt sie.
- **Rand ist eine eigene Ebene.** Stanzung und Abnutzung greifen nur ins Motiv, tragen dort aber
  die Milchigkeit des Rands; die Schmutzkante bleibt außen, Holo liegt über allem. `HANDOFF.md` 3d.
- **Die Blockwahl** (alle sechs Blöcke, 01 und 06 eingeschlossen) läuft über `focus` + `BLKFLAG`;
  **nur die Titelzeile** wählt und klappt zu (Zuklappen nur im mobilen Layout und nur wenn der
  Block schon markiert ist — die Blockfläche selbst darf nicht auslösen, sonst springt sie beim
  Scrollen). Aus|An ist ein Paar aus zwei Flächen, das Schloss friert einen Block ein
  (`locks[k]`) und muss auch `activeTool()` und `gestureMode()` sperren, nicht nur die Regler.
  **Zustandsgetriebene Optik gehört in CSS + `data-*`-Attribut, nie in `el.style.*`** — der
  Renderer besitzt das `style`-Attribut und macht imperative Schreibzugriffe rückgängig. Was **ein** Zeiger auf dem Sticker zieht,
  entscheidet `activeTool()`; was **zwei Finger** tun, entscheidet `gestureMode()` — das liest
  `focus`, nicht `activeTool()`. Anzeige und Ziehziel müssen dieselbe Regel lesen.
- **Feinkorrektur:** Der Strich ist interpoliert, die Vorschau (`_ba`) ist während des Zugs die
  einzige Wahrheit, und die volle Kette läuft erst 260 ms nach dem Absetzen. **Jede neue Aktion,
  die `composite()` liest, muss vorher `flushStroke()` rufen** — sonst liefert sie den Stand vor
  dem letzten Strich. Details und die Undo-Speichergrenze: `HANDOFF.md` 3b.
- **Mobil:** `LAYOUTS` (`wide`/`land`/`tall`) wird in `measureLayout()` aus der **gemessenen**
  Fenstergröße gewählt, nicht aus einer Media Query. Die Zeile darf **nie** umbrechen
  (`flexWrap: nowrap`) und die Desktop-Schwelle ist 900px, weil Bühne (520) und Panel (360)
  darunter nicht nebeneinander passen. Die Bühne hält `touch-action: none`, gescrollt wird über
  die vier Randstreifen und die zwei Fingerbalken; deren Abstände kommen aus `placeCaps()`,
  nie aus geratenen Konstanten. Details: `HANDOFF.md` 8.
- **Export:** Format (`exportFmt`) und Größe (`exportScale`) sind getrennt; die beiden
  WebP-Voreinstellungen `wa`/`tray` sind **feste Quadrate** (`expBox` → `fitBox`), keine
  Faktoren, und die Qualität wird gestuft bis unter die Grenze. Jeder Wechsel muss die gehaltene
  Teilen-Datei verwerfen. `HANDOFF.md` 3h.
- **Die Platte ist der Sticker-Körper, nicht die Freistellung.** `alphaC()` bleibt der Umriss des
  Motivs (nur das gedruckte Bild liest sie), `plateC()` ist das Rechteck, `bodyC()` wählt. Alles,
  was die **Form** betrifft — Distanzfeld, Silhouette, Glitter — liest `bodyC`. `HANDOFF.md` 3g.
- **Kapselmaße misst man einen Frame später** (`placeCaps` → `placeCapsNow`), sonst steht der
  Fingerbalken unter der Kapsel. Und ein Tippen auf eine Reglerschiene darf nie einen Wert setzen
  (`onSliderGrab`, 30 px um den Griff) — nicht über `pointer-events` lösen. `HANDOFF.md` 8.
- **Ein neuer Zustandswert braucht bis zu drei Einträge:** `state` (Standard), `derived` in
  `syncUI()` (wenn die Oberfläche ihn ableitet — `renderVals()` wird von `syncUI` **nicht**
  gelesen) und `renderVals()` (nur für `{{ }}`-Holes und Behandler). `HANDOFF.md` 5a.
