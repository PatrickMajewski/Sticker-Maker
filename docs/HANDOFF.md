# Sticker Maker — Handoff & Entscheidungsprotokoll

Stand: 3. August 2026 — **v1.2, vom Nutzer als Final Release freigegeben und eingefroren.**
Geschrieben für eine Übernahme in ein anderes Projekt oder durch eine andere KI. Wer hier
anfängt, braucht nichts weiter zu suchen: alle Quellen, Entscheidungen und bekannten Grenzen
stehen unten.

**Nächste geplante Etappe des Nutzers: eine echte mobile App.** Dafür ist Abschnitt 7 der
Einstieg — die Compositing-Pipeline wird 1:1 übernommen, die Oberfläche neu gebaut.

## 0. Versionen

Der Nutzer hat die Zählung bei der ersten Freigabe auf **v1.1** umgestellt (die älteren `v1`–`v3`
sind Zwischenstände aus der Entwicklung, nicht Vorgänger-Releases).

| Datei | Stand |
| --- | --- |
| `Sticker Maker v1.2 (Final Release).dc.html` | **freigegeben 03.08.2026** — v1.1 plus Dunkelmodus, Zoom-Reparatur, Besitzer-Wächter |
| `Sticker Maker v1.2 (Backup).dc.html` | identische Sicherung, damit Experimente (z. B. weitere KI-Modelle) den Release nicht gefährden |
| `Sticker Maker v1.2 (standalone).html` | Einzeldatei-Bundle des Final Release (661 KB, keine externen Abrufe) |
| `Sticker Maker v1.1 (Backup).dc.html` | vorheriger Release — vier Modi, Formwahl, Zoom, Glitter, Text-Layer, freier Zuschnitt; noch ohne Dunkelmodus |
| `Sticker Maker v2 (Backup).dc.html` | vor Pfad-Werkzeug, Zoom und Formwahl; hatte noch den Modell-Umschalter |
| `Sticker Maker v1 (Backup).dc.html` | vor Folien-Optik (Milchig/Schmutzkante) und Crop-Umbau |
| `fonts.css` | die zehn Textschriften als eingebettete `data:`-WOFF2 (OFL/Apache) |

Entwicklungsreihenfolge in Stichworten: Grundgerüst mit KI-Freistellung, Rand und Used Look →
Folien-Optik für den transparenten Rand → Zuschnitt- und Ecken-Schritt vor der Erkennung →
Holo nach Fotoreferenzen (Splitter, dann gemeinsames Regenbogenfeld) → Standalone-Export →
Modell-Umschalter (wieder ausgebaut) → Pfad-Werkzeug → Zoom & Navigation → Formwahl →
Freigestellt-Modus → Glitter → Text-Layer → freier Zuschnitt → Kontur- und Farblogik im Text →
**v1.1 eingefroren** → Dunkelmodus (aus dem Playlist Designer zurückportiert) → Zoom-Reparatur →
Besitzer-Wächter → **v1.2 eingefroren**.

---

## 1. Was das Ding ist

Browser-Werkzeug, das aus einem Bild einen druckreifen Sticker als transparentes PNG macht.
Drei Wege, alle im selben Screen:

**A — Vollflächig (Standard, ohne KI):** Bild laden → Zuschnitt (Format frei/1:1/4:5/3:4/16:9/9:16,
Zoom, Rahmen ziehen) → *Zuschnitt übernehmen* → Form wählen (**Eckig** oder **Gerundet** mit
Radius-Regler, 100 % = Pille/Kreis) → Rand → Used Look → Holo → Export.

**D — Freigestellt (`mode: 'cut'`, ohne KI):** Für fertig freigestellte PNGs. `readAlpha()` nimmt
den Alphakanal der Datei unverändert als Maske — keine Erkennung, kein Zuschnitt, direkt in den
Bearbeiten-Schritt. Pinsel wie bei der Erkennung, *Auf PNG zurücksetzen* liest das Alpha neu.
Hat die Datei keine Transparenz (>99,5 % deckend), sagt ein roter Hinweis das statt still ein
Rechteck zu bauen.

**B — Pfad (manuell, ohne KI):** Punkte ins Bild klicken, gerade Linien dazwischen, Form
schließt automatisch. Punkt ziehen justiert, Klick auf eine Linie setzt einen Punkt dazwischen,
Zähler und Buttons (letzten zurück / Punkt löschen / alle löschen). *Auswahl übernehmen* macht
daraus die Maske. „Pfad bearbeiten" holt die Punkte zurück. Entstand, weil die Erkennung bei
manchen Motiven willkürliche Ergebnisse liefert.

**C — Motiv erkennen (optional, mit KI):** Bild laden → Freistellung durch ein
Hintergrund-Entfernungs-Modell im Browser → Kantenweichheit/Einzug → Pinsel zum Nachbessern →
ab da identisch mit A.

Danach in beiden Wegen:
- **Rand** in zwei Modi: **weiß** (klassischer Sticker-Rand) oder **transparent**
  (Klarfolien-Look). Breite, plus „Kontur glätten" für einen Die-Cut-Umriss statt exakter Motivkontur.
- Nur bei transparentem Rand: **Folien-Optik** — *Milchig* (macht die klare Fläche sichtbar) und
  *Schmutzkante* (dunkelt ungleichmäßig nur den äußersten Saum ab, „Dreckfänger").
- **Used Look**: Kratzer / Abrieb / Knicke (prozedural, würfelbar) oder eigene Maske als Bild.
  Skalieren, drehen, spiegeln, auf dem Sticker ziehen, Stärke. **Die Kratzstellen folgen der
  Randwahl:** weißer Rand → weiße Kratzer, transparenter Rand → Löcher.
- **Holo-Effekt**: *Regenbogen* (breiter Pastellverlauf über die Fläche) + Textur-Wahl
  **Splitter | Glitter** + Größe + Dichte bis 500 %.
  *Splitter* = Broken-Glass-Flakes, je eine flache Farbe, als Folie über dem ganzen Sticker.
  *Glitter* = feine Körnung (bis 400 000 Körner via ImageData), jedes Korn nimmt seinen Farbton
  aus demselben Regenbogenfeld, Helligkeit variiert pro Korn, ~8 % blitzen fast weiß auf,
  dazu größere Funkelpunkte. Beim Glitter liegen Regenbogen und farbige Körnung **nur auf dem
  Motiv**; der weiße Die-Cut-Rand trägt dieselbe Körnung entgesättigt (Klarglitter über der Folie)
  — nach Referenzbild `uploads/glitter-stickers-rainbow-06@2x-3448561095.jpg`.
- **Export** PNG mit Transparenz in 1× / 2× / Originalauflösung.
- **Text** (Sektion 05): mehrzeilig, zehn kuratierte Schriften (je eine pro Sticker-Charakter,
  alle Google Fonts unter OFL/Apache — kommerziell nutzbar, Verkauf erlaubt) plus
  **eigene Schrift laden** (TTF/OTF/WOFF über die FontFace-API, kein Upload irgendwohin).
  Farbe Weiß / Schwarz / **Stanzen** (Text als Loch durch den Sticker), Deckkraft, Größe,
  Drehung, Laufweite, weiße Kontur, Ziehen auf dem Sticker.
  Toggle **Mit altern | Obendrauf**: `textAbove=false` zeichnet den Text direkt nach dem Motiv,
  also unter Used Look und Holo (er wird abgenutzt und glitzert mit); `true` zeichnet ihn als
  letztes über alles. Der Text ist immer auf `silC` geclippt, kann also nicht überhängen.
- Vorschau-Hintergrund: Schachbrett / dunkel / hell / eigenes Foto. Die beiden benannten Gründe
  stehen auf **festen** Werten (`#1c1b1a` / `#f4f2f0`) — sie sind Prüfgründe für den Freisteller
  und dürfen dem Oberflächenthema nicht folgen. Das Schachbrett folgt, weil beide seiner Stufen
  gemeinsam kippen und es „hier ist nichts" bedeutet, keine bestimmte Helligkeit.
- **Dunkelmodus** über einen Mond/Sonne-Knopf im Kopf (Prop `defaultTheme`). Details unter 2a.
- **Zoom & Navigation**: −/Wert/+ in der Bühnenleiste (Klick auf den Wert = einpassen),
  Strg/Cmd + Scrollen zoomt auf den Mauszeiger, mittlere Maustaste verschiebt, ab >100 %
  erscheinen Scrollbalken. Der Zoom sitzt auf der CSS-Breite des Canvas, damit die
  Treffererkennung aller Werkzeuge (`ptToCanvas`) unverändert weiterfunktioniert.

---

## 2. Quellen

**Design-Referenz (verbindlich):** `uploads/Spotify Playlist Designer App/`
- `README.md` — vollständiger Handoff der Referenz-App des Nutzers (iOS-Einzelscreen,
  Bild-Compositing, Masken-Pipeline). Enthält die Designtokens und die bewussten
  Radius-Abweichungen vom Design-System, die dieses Projekt übernimmt.
- `reference/Spotify Playlist Designer.dc.html` — Markup-Vorlage für Karten, Sektionsköpfe,
  `.seg`-Umschalter, Slider-Zeilen, Statuszeile, Schachbrett-Canvas, Lucide-Inline-SVGs.
- `reference/styles.css` — Tokenliste.
- Der Nutzer verlinkte die App zuerst als claude.ai-Share-Link; der ist login-geschützt und
  nicht abrufbar. Maßgeblich sind die Dateien in `uploads/`.

**Design-System:** `_ds/modernist-0d821174-d771-4d38-b122-5f85c61e0fea/styles.css` (Modernist —
Archivo, rot `#ec3013` auf hellem Grund `#f3f2f2`, flach, 2px-Regeln, Radius 0).
Die App überschreibt Radien absichtlich (siehe `CLAUDE.md`), weil die Referenz-App das tut.

**Effekt-Referenzen des Nutzers (Fotos echter Folien):**
- `uploads/il_1080xN.4694370326_n140-878874628.jpg` — Splitter-/Broken-Glass-Holofolie:
  klare Folie, verstreute scharfkantige Splitter, jeder in **einer** leuchtenden Farbe,
  ein Teil milchig-hell (unbeleuchtet). Vorlage für die Splitter.
- `uploads/StickersAoTErwin-501308420.png` — dieselben Flakes im Hintergrund hinter einem
  Sticker; zeigt zusätzlich den weißen Die-Cut-Rand.
- `uploads/il_600x600.6797920873_rbjg-2304278785.jpg` — breiter, weicher Pastell-Regenbogen
  über einem ganzen Print. Vorlage für den *Regenbogen*-Regler.
- `uploads/glitter-stickers-rainbow-06@2x-3448561095.jpg` — Rainbow-Glitter-Sticker im Makro:
  feine Körnung mit Eigenvariation, Farbe folgt dem Regenbogen, weißer Rand nur klar glitzernd.
  Vorlage für die Glitter-Textur.

**Modelle / Bibliotheken (zur Laufzeit vom CDN, nichts davon liegt im Projekt):**
- transformers.js — `https://cdn.jsdelivr.net/npm/@huggingface/transformers@3.5.2`
  (mit Fallback-URLs `/+esm` und unpkg).
- `briaai/RMBG-1.4` — **das einzige Erkennungsmodell.** Braucht `config: {model_type:'custom'}`
  und die handgeschriebene Prozessor-Konfiguration `PROC_CFG` (1024², mean/std 0.5/1.0), weil das
  Repo keine `preprocessor_config.json` hat. Eingang `input`, `dtype: 'q8'`. **Lizenz: nur
  nicht-kommerziell frei; kommerziell nur mit BRIA-Vertrag.**
- Geprüft und **wieder ausgebaut**: `onnx-community/BiRefNet_lite-ONNX` (MIT). War kurz als
  zweites Modell samt Umschalter eingebaut; der Nutzer hat es nach eigenem Test als unbrauchbar
  verworfen (willkürliche Masken), dazu ~120 MB WASM-Anforderung, an der speicherarme Browser
  scheitern. `MODELS` ist aber weiter ein Registry — ein zweites Modell wäre ein Eintrag plus
  ein Segmented-Control. Ebenfalls verworfen: `BiRefNet_512x512-ONNX` (gleicher Speicherfehler),
  quantisierte BiRefNet-Varianten (existieren nicht), U²-Net (schwächere Kanten),
  MODNet (nur Porträts).

---

## 2a. Dunkelmodus

Aus dem Playlist Designer zurückportiert (dessen `README.md` liegt unter
`uploads/Spotify Playlist Designer App für CLAUDE/`). Weil die ganze Oberfläche aus
`var(--color-*)` malt, ist der Dunkelmodus **ein Satz Variablen-Überschreibungen auf dem
Wurzelelement** — kein zweites Stylesheet, keine `.dark`-Klassen, keine Media Query. Die Map
heißt `DARK`, `applyTheme()` setzt bzw. entfernt sie und läuft aus `componentDidMount` und
`componentDidUpdate` neben `applyLang()`.

Vier Fallen, alle hier durchlebt — **wer die Map anfasst, muss sie kennen:**

1. **Die Akzentrampe muss mitgespiegelt werden, nicht nur die Neutralrampe.** Die Regel des
   Design-Systems lautet „eine Stufe hinter der Basis: 600 auf hellem, 400 auf dunklem Grund".
   Unmirrored steht `--color-accent-700` (Abschnittsnummern) mit **2,45:1** auf dem dunklen
   Grund. Danach 8,4:1.
2. **Auch die hellen Stufen 100/200.** Die trägt niemand als Schrift, sondern als *Fläche*:
   `.tag-accent` ist `background: accent-100; color: accent-800`. Bleiben sie hell, wird die
   Schrift korrekt hell und die Fläche fast weiß — gemessen **1,39:1** und optisch ein greller
   Fleck. Betroffen waren das ✓-Kennzeichen und der „keine Transparenz"-Hinweis.
   *(Diese Falle steht **nicht** in der Rückmeldung des Playlist Designers — dort ist sie noch
   offen; die Notiz dazu liegt unter `export/Spotify Redesign Kit/`.)*
3. **Farbe auf Akzentfüllung darf nicht `var(--color-bg)` sein** — im Dunkelmodus ergäbe das
   schwarze Schrift auf Rot. Überall festes `#f8f4f4`: Segment-Umschalter, jeder
   `.btn-primary`, alle Icon-Knöpfe, die aktiv auf `btn-primary` wechseln. Icons folgen über
   `currentColor`.
4. **`color-scheme: 'dark'` gehört in die Map** (eine gewöhnliche CSS-Eigenschaft, derselbe
   Loop setzt sie mit). Ohne sie malt der Browser Scrollbalken und Slider-Bahnen hell — bei dem
   langen Einstellungs-Panel ein deutlich sichtbarer heller Streifen. Zusätzlich auf `<html>`,
   und `document.body` beim Umschalten mitfärben, sonst blitzt es beim Überscrollen; beides in
   `componentWillUnmount` zurücksetzen.

**Nicht** dem Thema folgen dürfen die Vorschau-Gründe „Dunkel"/„Hell" (siehe Abschnitt 1).

Mit portiert: die **`data-off`-Konvention** — Karten-Umschalter (Rand-Modus, Eckig/Gerundet,
Textfarbe) tragen `data-off="var(--color-surface)"`, damit `syncUI()` ihren Aus-Zustand auf die
Oberflächenfarbe statt auf transparent setzt; im Dunkelmodus war der Unterschied sichtbar.

Und der **Besitzer-Wächter**, ebenfalls von dort: ein Code-Reload baut eine neue Instanz,
während das alte Wurzelelement samt Listenern überlebt — beide schreiben danach in dieselben
Knoten. `rootRef` schreibt `el.__dcOwner = this`, `owns()` prüft es, und `syncUI`, `applyLang`,
`applyTheme`, `applyView` und `draw` steigen sonst sofort aus. Der Bühnen-Halter leert zusätzlich
fremde Kinder, bevor er das Canvas anhängt.

---

## 3. Architektur

Der Kern ist **maskengetrieben**. `this.maskC` ist eine Graustufen-Canvas in Arbeitsauflösung,
weiß = Sticker. Alles danach liest nur diese Maske:

```
maskC ──► alphaC(k)   weiche Alphakante (Blur + contrast/brightness als Levels)
          │           Regler: Kantenweichheit, Kante einziehen/erweitern
          ├─► cutC(k) Motiv mit Alpha
          └─► silC(k) Sticker-Silhouette = Maske um Randbreite dilatiert (30+14 versetzte
                      Stempel) + geglättet (Blur + Alpha-Stapeln), weiß gefüllt
                      │
                      ├─► rimC(k)  Schmutzkante: sil minus erodiertes sil = schmales Band,
                      │            × Fleckenrauschen (blotchC), dunkel gefärbt
                      ├─► usedC(k) transformierte Used-Maske, per Schwelle binarisiert,
                      │            auf sil geclippt
                      └─► holoField(k) ─► washC(k)  Pastell-Regenbogen (Blend `color` + `screen`)
                                       └─► holoC(k) Splitter, Farbe = Position im Feld
```

`composite(k)` setzt das in dieser Reihenfolge zusammen: weißer Rand **oder** Milchig+Schmutzkante,
dann Motiv, dann Used Look (weiß aufmalen bzw. `destination-out`), dann Holo (geclippt aufs
Ergebnis). `k` ist der Auflösungsfaktor — 1 für die Vorschau, beim Export 1/2/Original.
Caches gelten nur für `k === 1`.

**Maskenquellen:** `runModel(key)` (KI), `chromaCut()` (Offline-Flutfüllung von den Bildrändern
mit Toleranz), `paintAt()` (Pinsel), `rectMask()` (Rechteck mit runden Ecken).

**Warum kein `{{ }}`-Hole für Text/Stil/Werte:** siehe `CLAUDE.md`. Kurz: sonst bleibt die
Oberfläche während des Streamens leer und Nutzer können Texte im Editor nicht anfassen.

---

## 4. Entscheidungen und ihre Gründe

| Entscheidung | Grund |
| --- | --- |
| Vollflächig ist der **Standard**, KI ist der Zusatzschritt | Ausdrücklicher Wunsch: „die Bilder nicht zwangsweise durch die Erkennung schleusen" |
| Zuschnitt mit Format + Zoom + Ziehen, dann *Übernehmen* | Nutzer wollte „pauschal auf Maß croppen" mit Bestätigung, erst danach Ecken |
| Ecken als Prozent der halben kurzen Seite | 100 % ergibt Pille/Kreis, skaliert mit jedem Format |
| Rand per Stempel-Dilatation statt Kontur-Offset | funktioniert auf beliebigen Masken, auch mit Löchern; keine Pfad-Geometrie nötig |
| Glättung als Blur + Alpha-Stapeln | rundet Einbuchtungen wie ein echter Die-Cut, GPU-nah, kein Pixel-Loop |
| Kanten immer mit ≥0,35 px Blur | Vorgabe „keine harten Pixel" |
| Schmutzkante = 16 % der Randbreite, hartes Band + kurzer Auslauf | Nutzer: „wirklich die äusserste Umrandung die zum Dreckfänger wird", nicht die Fläche |
| Splitter je **eine** flache Farbe | Nutzerkorrektur; Verläufe pro Splitter sahen falsch aus |
| Pfad-Werkzeug mit geraden Linien statt Kurven | Nutzeridee, nachdem BiRefNet willkürliche Ergebnisse lieferte; gerade Linien + „Kontur glätten" reichen für Sticker |
| Vierter Modus „Freigestellt" nimmt PNG-Alpha unverändert | Nutzer mit Vorarbeit soll nur Ränder/Effekte brauchen, ohne Erkennung und ohne Quadrierung |
| Glitter zeichnet in ImageData, nicht per `fillRect` | bis 400 000 Körner — Canvas-Aufrufe wären um Größenordnungen zu langsam |
| Bei Überlappung gewinnt das hellere Korn | sonst matscht hohe Dichte zu Grau statt zu funkeln |
| Glitter-Regenbogen nur aufs Motiv, Rand nur entgesättigt | Referenzbild: der Druck trägt die Farbe, die Folie über dem weißen Rand glitzert klar |
| Schriften kuratiert (10 nach Charakter) statt Katalog-Suche | Entscheidung in Sekunden statt Scrollen; Lizenzfilter OFL/Apache vorab, damit Verkauf unbedenklich ist |
| „Eigene Schrift laden" als Ventil | hält die Auswahl unbegrenzt, ohne dass die Oberfläche eine Bibliothek wird |
| Text unter den Effekten als **Standard** | Nutzer: die Schrift ist Teil des Stickers und soll mitaltern; „Obendrauf" bleibt für Schlagzeilen |
| Holo liegt **nicht** auf den Kratzstellen | Nutzerhinweis: eine Kratzstelle ist abgetragene Folie — dort ist der Glanz weg. Die Used-Maske wird aus der Holo-Clipmaske abgezogen (bei transparentem Rand entfällt das, weil dort gestanzt wird) |
| Pfadpunkte werden bei Bildwechsel, Zuschnitt und Moduswechsel verworfen | sie sind absolute Koordinaten in der Arbeitsauflösung und würden sonst versetzt liegen |
| Zoom über CSS-Breite, nicht über Canvas-Transform | so bleibt `getBoundingClientRect`-Mapping korrekt; Werkzeuge brauchen keine eigene Zoom-Mathematik |
| Fit-Basis nur ohne Scrollbalken messen | sonst schrumpft die Bühne beim Erscheinen der Balken und die Prozentanzeige driftet |
| Ein gemeinsames `holoField` für Wash und Splitter | Nutzerwunsch: der Regenbogen soll die Flakes beeinflussen, nicht daneben liegen |
| Wash mit Blend `color` (+ leicht `screen`) | tönt den Print, ohne Zeichnung zu zerstören — wie Folie über Papier |
| Leere/vollständige Modellausgabe wird abgefangen | RMBG liefert bei flächigen Grafiken eine Nullmaske; früher gab es dafür „Erfolg" und ein leeres Canvas |
| Modell-Umschalter mit Fallback-Kette | Lizenzfrage (BRIA nicht-kommerziell) und Vergleichbarkeit an eigenen Motiven |
| Umschalter danach **entfernt**, nur RMBG 1.4 bleibt | Nutzertest: BiRefNet lieferte willkürliche Masken. Eine Wahl weniger, ehrlicher Hinweis statt Scheinoption |
| App auf Viewport-Höhe fixiert, nur das Panel scrollt | Desktop-Werkzeug-Gefühl; unter ~860 px stapelt es ohne Media Queries (Flex-Wrap) |

---

## 5. Bekannte Grenzen

1. **BiRefNet lief in der Vorschau-Umgebung nicht** und ist wieder ausgebaut.
   ONNX-Runtime brach mit einer Speicheranforderung von ~122 MB ab (Fehler erscheint als nackte
   Zahl `127873152`); die 512×512-Variante scheiterte identisch, quantisierte Varianten gibt es
   nicht. Unabhängig davon waren die Masken laut Nutzer unbrauchbar. Es gibt jetzt **nur
   RMBG 1.4**; schlägt es fehl, greift die Offline-Freistellung. **Nach einem Speicherabbruch ist
   der Tab vergiftet** — die Statuszeile bittet um Neuladen.
2. **RMBG-1.4-Lizenz.** Frei für nicht-kommerzielle Nutzung; für Verkauf von Stickerpacks nach
   üblicher Lesart nicht ausreichend (keine Rechtsauskunft). Der vollflächige Weg nutzt kein
   Modell und ist davon unberührt.
3. **Erster Start braucht Internet** (Modell-Download, danach Browser-Cache). Ohne Netz
   funktioniert alles außer der KI-Erkennung; dort greift die Offline-Flutfüllung.
4. **Arbeitsauflösung ist auf 1500 px lange Kante gedeckelt** (Prop `workResolution`, 600–2400).
   Export „Original" rechnet auf die Originalgröße hoch, die Maske stammt aber aus der
   Arbeitsauflösung.
5. **Holo bei Dichte 500 % + großen Splittern** rechnet bis 12 000 Formen — kurzes Stocken möglich.
   Glitter bei 500 % + feiner Körnung erzeugt bis 400 000 Körner; in der Vorschau ~0,3 s, beim
   Export mit 2× entsprechend mehr.
6. Zuschnitt hat **keine Ziehgriffe** an den Rahmenecken (bewusst: Format + Zoom + Ziehen), und
   **keine mm/Druckmaße**. Beides war angeboten und ist offen.
7. **Kein Pinch-Zoom auf Touch.** Die Bühne braucht `touch-action: none` für die Werkzeuge
   (Punkte ziehen, Pinsel, Maske verschieben); Zoom läuft dort über die −/+-Knöpfe.
   Zwei-Finger-Pinch wäre nachrüstbar, braucht aber eigene Pointer-Buchhaltung.
8. **Pfad hat nur gerade Linien** — keine Bezier-Kurven. Rundungen kommen über
   „Kontur glätten" im Rand-Abschnitt.
9. **Die zehn Textschriften sind eingebettet** — `fonts.css` trägt sie als `data:`-URI
   (Basic-Latin-Subset, WOFF2, 224 KB Schriftdaten → 300 KB CSS). Text funktioniert damit
   offline, ohne Google-Fonts-Abruf, und die Standalone-Datei enthält sie mit.
   Neu erzeugen: die zehn Familien von `fonts.googleapis.com/css2` holen, pro Familie den Block
   mit `unicode-range: U+0000-00FF` nehmen, dessen WOFF2 laden und base64 in eine
   `@font-face`-Regel schreiben (das hat ein `run_script` getan; gstatic erlaubt CORS).
10. **Nur eine Textebene.** Mehrere Zeilen ja, mehrere unabhängig platzierte Texte nein.

---

## 6. Offene Angebote an den Nutzer

- Echte PWA (Manifest, Icon, Offline-Cache), damit „Zum Home-Bildschirm" sich wie eine App verhält.
- Pinch-Zoom und Zwei-Finger-Pan auf Touch.
- Bezier-Kurven im Pfad-Werkzeug.
- Mehrere Textebenen.
- Frei wählbare Konturfarbe im Text (statt der Automatik).
- Ziehgriffe im Zuschnitt statt der Breite/Höhe-Regler.
- Speicherumschlag beim Zusammensetzen entschärfen — aufgeschoben, Befund in 6b.

## 6a. Ein weiteres KI-Modell einbauen

Die Erkennung ist als Registry gebaut, damit genau das ohne Umbau geht. Vier Stellen in
`Sticker Maker.dc.html`:

1. **`MODELS`** (Konstante oben in der Logik) — ein Eintrag pro Modell:
   `{ id: 'org/repo', label: 'Anzeigename', cfg: <Prozessor-Config oder null>, modelCfg: { model_type: 'custom' }, dtypes: ['q8', null] }`.
   `cfg: null` heißt: das Repo bringt eine eigene `preprocessor_config.json` mit.
   `dtypes` wird in dieser Reihenfolge probiert, `null` = unquantisiert.
2. **`runModel(key)`** braucht nichts — es liest Ein- und Ausgangsnamen aus der Session
   (`inputNames[0]`, dann `output || output_image || alphas || erster Wert`) und fängt Logit-Ausgaben
   per Sigmoid ab. Nur wenn ein Modell davon abweicht, hier ergänzen.
3. **`segment()`** setzt derzeit `const want = 'rmbg';` — daraus wieder
   `MODELS[this.state.model] ? this.state.model : 'rmbg'` machen und `model: 'rmbg'` in `state`
   als Auswahl führen. Die Generationszählung (`this._gen`) muss bleiben: ein langsamer Lauf darf
   das Ergebnis eines neueren nicht überschreiben, und die Auswahl des Nutzers darf **nie**
   von der Logik zurückgeschrieben werden (das war ein echter Bug).
4. **Template**: Segmented-Control mit `data-seg="model" data-val="<key>"` in den
   `data-show="isAi"`-Block, plus in `onSeg` den Zweig
   `if (k === 'model') { this.setState({ model: v }, () => … segment()); return; }`.
   Der Code dafür steht unverändert in `Sticker Maker v2 (Backup).dc.html`.

Getestet und verworfen wurde BiRefNet (siehe Abschnitt 2 und Grenze 1) — wer ein Modell
probiert, sollte an **einem** Motiv gegen RMBG vergleichen und auf den Speicherbedarf achten:
scheitert ONNX-Runtime an der Allokation, ist der Tab danach unbrauchbar und muss neu geladen
werden.
- Ziehgriffe oder feste Druckmaße im Zuschnitt.
- Schmutzkante wärmer/bräunlich statt neutral grau (eine Zeile: Füllfarbe in `rimC`).
- Regenbogen auf genau einen Durchlauf festnageln statt 0,8–2,4 Zyklen.
- Splitter in halber Auflösung rechnen, falls die höchste Dichte zu träge wird.

---

## 6b. Befund: Speicherumschlag beim Zusammensetzen (aufgeschoben, nicht erledigt)

Aufgenommen am 03.08.2026, bewusst **nicht** umgesetzt: die App ist eingefroren und läuft auf
dem Rechner flüssig genug. Der Nutzer will das Risiko ohne Not nicht eingehen und muss zuerst
auf einem iPhone 14 Pro Max testen. **Wenn Mobilbetrieb dazukommt, ist das der erste Punkt** —
Speicherdruck ist dort deutlich spürbarer als auf dem Desktop.

**Befund.** Die teuren Ableitungen sind über `invalidate()` sauber zwischengespeichert. Nicht
zwischengespeichert sind die Beschneidungsschritte **innerhalb von `composite()`**, das bei
jeder Änderung komplett neu läuft. Im Vollausbau (Glitter + Regenbogen + Abnutzung, weißer Rand)
entstehen dort **sechs volle Canvas in Arbeitsauflösung pro Einzelbild**, die sofort wieder
verworfen werden: `motif`, `cl` (Motiv minus Kratzstellen), `clip(washC)`, `clip(holoC)`,
`rim` (nur der Rand), `t` (Körnung auf den Rand beschnitten). Bei 1500 px Arbeitsauflösung sind
das je ~8 MB, zusammen **~47 MB Anforderung und Freigabe pro Bild** — beim Ziehen an einem
Regler sechzigmal pro Sekunde. Beim Export mit 2× vervierfacht sich die Größe je Canvas.
Das ist dasselbe Muster wie Punkt 4 der Rückmeldung aus dem Playlist Designer, nur dass es dort
Ebenen waren und hier Beschneidungsschritte.

**Lösungsweg.** Ein fester Satz wiederverwendbarer Arbeitsflächen statt Neuanforderung:

```js
scratch(n) {
  const key = '_sc' + n;
  let c = this[key];
  if (!c || c.width !== W || c.height !== H) { c = this.cv(W, H); this[key] = c; }
  const g = c.getContext('2d');
  g.setTransform(1, 0, 0, 1, 0, 0);
  g.globalCompositeOperation = 'source-over';
  g.globalAlpha = 1; g.filter = 'none';
  g.clearRect(0, 0, c.width, c.height);
  return c;
}
```

Vier Plätze genügen: 1 = `motif`, 2 = `cl`, 3 = `clip()`-Ergebnis **und** `t` (das
Regenbogen-Ergebnis ist gezeichnet, bevor die Körnung drankommt — sie teilen sich den Platz),
4 = `rim`. Ergebnis: statt ~47 MB Umschlag pro Bild einmalig ~32 MB, die stehen bleiben.
Nach einem Export in höherer Auflösung die Flächen freigeben (`this._sc1 = … = null`), sonst
bleibt dauerhaft die vierfache Größe belegt.

**Die eine Falle, und wie man sie ausschließt.** Eine wiederverwendete Fläche erbt Transform,
Deckkraft, Filter und Mischmodus vom vorigen Gebrauch. Das Zurücksetzen gehört deshalb **in die
Ausgabefunktion**, nicht an die Aufrufstellen — dann gibt es keinen Zweig, der es vergessen
kann. Der Fehler wäre sonst doppelt heimtückisch: er zeigt sich erst beim **zweiten** Aufruf
(der erste bekommt eine frische Fläche) und nur in **bestimmten Effektkombinationen**. Ein
einzelner Testexport sieht auch dann sauber aus, wenn das Zurücksetzen ganz fehlt.

**Prüfung, falls umgesetzt.** Nicht ein Bild vergleichen, sondern mehrere Effektkombinationen
automatisch durchlaufen (Splitter/Glitter × Abnutzung an/aus × weißer/transparenter Rand ×
Text unter/über) und jede **zweimal hintereinander** rendern — nur die Wiederholung deckt den
Vererbungsfehler auf. Jedes Ergebnis gegen den heutigen Stand vergleichen.

## 7. Wenn du das in ein anderes Framework portierst (oder eine mobile App daraus machst)

Übernimm **`composite()` und die Ableitungen** unverändert — dort steckt die Arbeit
(Dilatation, Glättung, Levels auf dem Alphakanal, Schwellwerte, Blend-Reihenfolge). Das braucht
2D-Canvas mit Pixelzugriff und Blend-Modi (`destination-in`, `destination-out`, `source-in`,
`color`, `screen`). Die Oberfläche ist austauschbar; die Reihenfolge im Compositing ist es nicht.

Für eine native/mobile Umsetzung zusätzlich bedenken:
- **Gesten**: Pinch-Zoom und Zwei-Finger-Pan gehören dort selbstverständlich dazu (hier fehlen
  sie, weil die Bühne `touch-action: none` für die Werkzeuge braucht).
- **Modell**: RMBG 1.4 ist nicht-kommerziell lizenziert. Für eine App im Store, die verkauft
  wird oder mit der verkauft wird, vorher die Lizenzfrage klären oder ein Modell mit freier
  Lizenz einsetzen. Der vollflächige und der Pfad-Weg brauchen gar kein Modell.
- **Speicher**: Masken und Zwischen-Canvases liegen mehrfach in Arbeitsauflösung vor; auf
  Telefonen die Arbeitsauflösung (`workResolution`, Standard 1500 px) eher senken.
  **Vorher Abschnitt 6b lesen** — der Speicherumschlag beim Zusammensetzen ist auf dem Desktop
  vertretbar und wurde dort bewusst nicht angefasst, ist auf dem Telefon aber der erste
  Kandidat. Der Nutzer testet auf einem iPhone 14 Pro Max.
- **Bibliotheken**: React Native → `react-native-skia` + `react-native-gesture-handler`;
  iOS nativ → Core Image / `CIBlendWithMask`. Das steht so auch schon im README der Referenz-App.
