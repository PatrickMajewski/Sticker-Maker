# Sticker Maker — Handoff & Entscheidungsprotokoll

Stand: 12. August 2026 — **v1.4, vom Nutzer als Meilenstein freigegeben und eingefroren.**
Eine Datei bedient Telefon und Rechner. Der mobile Umbau steht in Abschnitt 8, die Bedienlogik
des Panels in 8a.
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
| `Sticker Maker v1.4 (Final Release).dc.html` | **freigegeben 12.08.2026** — v1.3.5 plus WebP-Export mit Formatreihe und den beiden Messenger-Voreinstellungen |
| `Sticker Maker v1.4 (Backup).dc.html` | identische Sicherung, damit Experimente (z. B. weitere KI-Modelle) den Release nicht gefährden |
| `Sticker Maker v1.4 (standalone).html` | Einzeldatei-Bundle des Final Release (773 KB, keine externen Abrufe) |
| `Sticker Maker v1.3.5 (Backup).dc.html` | vorheriger Meilenstein — letzter Stand ohne WebP |
| `Sticker Maker v1.3.1 (Backup).dc.html` | vorheriger Meilenstein — zweite mobile Runde |
| `Sticker Maker v1.3 (Backup).dc.html` | erste mobile Fassung — drei Layouts, Ecken-Kapseln, Fingerbalken, Zwei-Finger-Gesten |
| `Sticker Maker v1.2.3 (Backup).dc.html` | **letzter reiner Desktop-Stand** — der Vergleichspunkt, wenn eine mobile Änderung am Rechner etwas kaputt macht |
| `Sticker Maker v1.2.2 (Backup).dc.html` | vorheriger Release — Pinsel-Interpolation, Referenzbild, Strich-Undo |
| `Sticker Maker v1.2.1 (Backup).dc.html` | vorheriger Release — Distanzfeld-Rand, scharfe Kante, Zwei-Tipp-Löschen, Teilen-Menü |
| `Sticker Maker v1.2 (Backup).dc.html` | vorheriger Release — Dunkelmodus, Zoom-Reparatur, Besitzer-Wächter; Rand noch gestempelt |
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
Besitzer-Wächter → **v1.2 eingefroren** → Rückmeldung Runde 2/3 aus dem CD Case Creator ausgewertet
(fünf von sechzehn Punkten übernommen) → Rand auf Distanzfeld umgestellt und Kante geschärft →
**v1.2.1 eingefroren** → Pinsel auf Streckeninterpolation mit aufgeschobenem Neuaufbau, Referenzbild,
Strich-Undo, Abrieb steuerbar gemacht → **v1.2.2 eingefroren** → Blockwahl über die Titelzeile (Lampe als Anzeige), Folien-Optik in
Stanzung und Abnutzung, Erkennung erst auf Knopfdruck, Schmutzkante auf die Kontur gewichtet →
**v1.2.3 eingefroren** — letzter reiner Desktop-Stand → mobiler Umbau (Layouts, Ecken-Kapseln,
Fingerbalken, Zwei-Finger-Gesten), Regler-Drosselung, Pinsel-Stabilisierung, Schmutzkante als
abgeplatzte Kante neu gebaut → **v1.3 eingefroren** → Fehlerprotokoll und Unterdrückung des Bundler-Overlays, Sperre je Block,
Aus/An als Segmented, einklappbare Kapsel oben rechts, Export auf dasselbe Blockmuster →
**v1.3.1 eingefroren** → Platte als eigene Formquelle, Schachbrett-Metrik für eckige Randecken,
Kantenregler zur Erkennung gezogen, Reglerschutz gegen versehentliches Tippen, Feinkorrektur endet
beim Blockwechsel, Hintergrundgruppe einklappbar, Kopfleiste und Kapseln vereinheitlicht →
**v1.3.5 eingefroren** → WebP-Export mit Formatreihe, 512×512 für WhatsApp/Telegram und 96×96
Paketsymbol → **v1.4 eingefroren**.

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
  Der Umriss entsteht aus einem **Distanzfeld** (Abschnitt 3a) und ist bei jeder Breite exakt rund;
  die Kante ist ein Schnitt mit einem antialiasenden Pixel, kein Verlauf.
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

## 3a. Der Rand: Distanzfeld statt gestempelter Kopien

Bis v1.2 entstand die Silhouette, indem die Alphamaske dreißigmal im Kreis versetzt
übereinandergezeichnet wurde. Die Stempelmittelpunkte liegen `2πr/30 ≈ 0,21·r` auseinander —
ab etwa 20 px Randbreite ist die Kontur sichtbar facettiert, und „Kontur glätten" war das
einzige Gegenmittel, was die Kante gleich mit weichwusch.

Jetzt berechnet `edtField(k)` eine **exakte euklidische Distanztransformation** (Felzenszwalb &
Huttenlocher, eine lineare Passage je Achse, quadrierte Distanzen). Der Rand ist die Schwelle
darauf: `cov = clamp(r + 0.5 - dist, 0, 1)`. Exakt rund bei jeder Breite, mit einer Rampe von
genau einem Pixel.

**Drei Dinge, die dabei zu beachten sind:**

1. **Die Feldgeometrie darf nicht an der Randbreite hängen.** `padPx()` wächst mit `borderW`,
   das Feld wird aber absichtlich nicht bei `invalidate('border')` verworfen — sonst wäre der
   Cache-Gewinn dahin. Ein Feld in der jeweils aktuellen Größe würde nach jedem Reglerzug mit
   falscher Zeilenlänge indiziert (Rand verschert oder verschwindet komplett). Deshalb polstert
   `edtField` immer für `MAXBORDER` (= Regler-Maximum 70), legt `{W, H, p}` mit in den Cache,
   und `silC` liest über den Versatz `F.p − p` mit Geometrie-Wächter.
   **`MAXBORDER` und das `max` des `borderW`-Reglers müssen übereinstimmen.**
2. **Glätten heißt schwellen, nicht verdichten.** Nach `blur(sm)` stand früher `hard(b, 7)` —
   das zeichnet das Canvas achtmal auf sich selbst (`α' = 1−(1−α)⁸`) und verdichtet nur; kleine
   Alphawerte überleben als langer Schwanz, die Rampe wuchs proportional zum Weichzeichner
   (bei 50 % Glättung 45 px Verlauf). Jetzt wird auf die 50-%-Kontur geschwellt:
   `a = (a − 0.5)·sm + 0.5`. Ein Gauß mit Radius `sm` hat dort die Steigung ~1/sm, die
   Multiplikation zieht die Rampe auf einen Pixel zurück. Form rundet weiter aus, Kante bleibt Schnitt.
3. **Keine Breitenkompensation mehr.** `r = bw − sm/2` glich den Auswärtsdrift des Verdichtens
   aus. Die Schwelle lässt die Kontur stehen, also machte der Abzug den Rand nur schmaler als
   der Regler sagt (bei 70/100 % um 41 %). Jetzt `r = bw`.

**Und die Weichzeichner-Falle eine Ebene darüber:** `alphaC()` rechnete auf einem Canvas ohne
Rand. Eine Form, die die Canvas-Kante berührt — ein vollflächiges Rechteck tut das auf allen
vier Seiten —, hat dort nichts zum Hineinlaufen: die Geraden blieben hart, die innenliegenden
Ecken wurden voll weich, und der Übergang Bogen→Gerade saß sichtbar versetzt. `alphaC` rechnet
jetzt in einem Rand von `ceil(3b+2)` px **auf schwarzem Grund**, damit sich die Umgebung genau
wie das Maskenäußere verhält.

Zusammenhängend damit: **Kantenweichheit ist bei erzeugten Formen 0.** Eine gezeichnete,
geklickte oder mitgelieferte Kontur ist exakte Geometrie — die Kantenglättung des Canvas liefert
den einen sauberen Übergangspixel schon. `featherFor(mode)` setzt 0 für `full`/`path`/`cut`
und merkt sich den Wert der Erkennung (Standard 1,4) in `_aiFeather`.

---

## 3b. Die Feinkorrektur: Strich, Vorschau, Rücknahme

Drei Dinge greifen hier ineinander; wer eines davon anfasst, muss die anderen kennen.

**Der Strich ist interpoliert.** Ein Zeigerereignis kommt alle paar Millisekunden — bei zügigem
Ziehen liegen zwischen zwei Meldungen zig Pixel. `strokeTo()` setzt deshalb Tupfer entlang der
Strecke seit dem letzten Punkt, alle 30 % der Pinselbreite, und liest zusätzlich
`getCoalescedEvents()` aus (die Zwischenpunkte, die der Browser erfasst aber zusammengefasst hat).
Ohne beides zerfällt die Linie in Perlen.

**Während des Strichs wird nur eine leichte Vorschau gezeichnet.** `_ba` ist eine Kopie der
Alphaebene, die jeden Tupfer direkt mitbekommt; `drawStroke()` malt Referenz, Motiv und diese
Ebene — drei `drawImage`, kein Distanzfeld, kein Rand, keine Effekte. Die volle Kette läuft
**einmal, 260 ms nach dem letzten Absetzen** (`endStroke`). Ein neuer Strich sagt den anstehenden
Neuaufbau ab und malt auf derselben Vorschau weiter.

**Die Falle dabei:** Solange `_ba` existiert, ist `maskC` neuer als jeder abgeleitete Cache.
`draw()` verdeckt das, weil es aus der Vorschau malt — `composite()` würde aber den Stand
**vor** dem letzten Strich liefern. Alles, was die Wahrheit braucht, ruft daher zuerst
`flushStroke()`: Export, Moduswechsel, Bild entfernen, Feinkorrektur-Schalter.
**Wer eine neue Aktion baut, die `composite()` liest, muss sie ebenfalls dort eintragen.**

**Rücknahme:** Ein Strich ist ein Schritt. `pushUndo()` in `beginStroke()` legt die Maske als
**einzelnen Graukanal** (`Uint8Array`) ab, nicht als Canvas — die Maske ist Graustufen, drei von
vier Bytes wären Kopien. Bei 1500 px Arbeitsauflösung sind das 1,7 statt 6,8 MB je Schritt; zehn
Canvas-Kopien hätten auf dem Telefon gereicht, um den Tab abzuschießen. `clearUndo()` steht an
**jeder** Stelle, die die Maske komplett ersetzt (`rectMask`, `readAlpha`, `pathMask`,
Erkennungserfolg, Bild entfernen) — eine Momentaufnahme gehört zu einer Form, die es dann nicht
mehr gibt.

**Das Referenzbild** (`refOn`, Standard an) liegt mit 22 % Deckkraft hinter dem Sticker, solange
die Feinkorrektur läuft — auch während des Strichs, weil `drawStroke()` dieselbe `drawRef()` ruft.

---

## 3c. Der Abrieb

Der Abrieb hatte bis v1.2.1 einen unbrauchbaren Stärkeregler: Die Kerne jeder Zone waren voll
deckend gezeichnet, die Flecken stapelten sich zu Weiß. Die Schwelle in `usedC()` schaltete eine
fertige Fläche bei etwa 8 % ein und änderte danach nichts mehr.

Zwei Regeln beheben das und müssen zusammen bleiben:

1. **Jeder Fleck nimmt seine Helligkeit aus einem Gefälle über die Zone** (`lum(t)`,
   exponentiell abfallend). Die Schwelle frisst die Zone dadurch von innen nach außen auf.
2. **Die Flecken überlagern sich mit `lighten`, nicht durch Stapeln.** Bei Überlappung bleibt
   der hellere Wert stehen statt sich zu Weiß zu summieren — die klumpige, ausgefranste Geometrie
   überlebt, ohne deckend zu werden.

---

## 3e. Die Schmutzkante: abgeplatzte Kante statt Verlaufsbändern

Bis v1.3 waren es vier erodierte und weichgezeichnete Kopien der ganzen Silhouette — rund vierzig
Vollbild-Operationen, die zusammen wie ein gezeichneter Strich aussahen und den Block zum
teuersten der App machten. Die Referenz des Nutzers zeigt etwas anderes: **unregelmäßige
Ausbrüche direkt auf der Kontur**, hart begrenzt, in der Tiefe schwankend.

Jetzt entsteht der Ring in **einem** Durchgang über ein schmales Band, ohne jeden Weichzeichner.
**Drei** Ebenen Wertrauschen, und die Reihenfolge ist die Aussage:

1. Eine sehr grobe **Hüllkurve** (`cellE`, mindestens 60 px) entscheidet, **welche Strecken**
   überhaupt abgenutzt sind. Ohne sie bekam jede Stelle dieselbe Behandlung und das Ergebnis las
   sich als ein durchgehend unruhiger Saum; die Referenz hat lange ruhige Läufe und dann eine
   Strecke, die richtig weggefressen ist. Die Kurve wird geschwellt und weich verrundet
   (`smoothstep`), damit die Übergänge nicht als Kanten mitzeichnen.
2. Eine mittlere Ebene (`cellA`) bestimmt innerhalb einer abgenutzten Strecke die Tiefe, mit
   einer Potenzkurve, damit es kein Strich konstanter Breite wird.
3. Eine feine Ebene (`cellB`) zerfranst die Innenkante zu Krümeln. Dazu ein Haarstrich bei `t < 1.2`, damit die Schnittkante immer als
Kante liest.

**Vier Fallen, jede davon war ein echter Fehler:**

1. **Das Feld muss von der FERTIGEN Silhouette messen, nicht von der Freistellung.** Das
   Hauptfeld hat seine Kontur bei `dist = borderW` — sobald „Kontur glätten" sie verschiebt, ist
   das nicht mehr die sichtbare Kante, und der Schmutz malt seine eigene darüber.
2. **Ohne Glättung ist das zweite Feld überflüssig.** Dann *ist* die Silhouettenkontur die
   r-Isolinie des Hauptfelds, das ohnehin warm daliegt: `t = borderW − dist`. Das ist der
   Schnellpfad, und er spart den gesamten zweiten Durchgang.
3. **Der Schnellpfad gilt nur, solange `reach <= borderW`.** Jenseits der Motivkante ist das
   Hauptfeld flach null, der Abstand hört auf zu wachsen — und der Schmutz flutet den ganzen
   Sticker. Darüber wird das eigene Feld gebaut.
4. **Im eigenen Feld muss das Außen als negativ markiert werden.** Gesät wird außerhalb der Form,
   also kommt jeder Hintergrundpunkt mit `dist === 0` zurück und rutscht durch die Reichweiten-
   Prüfung: 87 % der Rauschschleife lief auf Punkten, die der Beschnitt danach wegwarf. Der
   Marker ist `-1000`, nicht `-1`, weil die Prüfung selbst bei `t < -1` liegt — der Ring darf
   einen Pixel über die Kontur hinaus zeichnen, sonst bleibt der äußerste Antialiasing-Pixel
   sauber und man sieht einen hellen Saum außerhalb des Schmutzes.

**Die Tiefe ist ein eigener Regler** (`grimeDepth`, 1–12 px), bewusst unabhängig von der
Randbreite, und darf aufs Motiv laufen. **`_rimF` hängt nicht an einer Verwerfungsebene**,
sondern an einem Schlüssel aus `borderW`/`smooth`/`borderOn`: die Schmutzregler und die
Milchigkeit können die Kontur nicht bewegen und dürfen das Feld nicht neu bauen.

---

## 3f. Der stabilisierte Pinsel

Der Strich lag direkt auf dem Zeiger, also war jedes Zittern von Hand oder Maus im Ergebnis.
Jetzt läuft die Spitze dem Zeiger hinterher: sie holt pro Ereignis 34 % des Abstands auf, was
das Zittern herausmittelt, und wird dann auf eine feste Höchstverzögerung nachgezogen
(`max(4, brushSize * 0.22)`), damit ein zügiger, gewollter Strich nicht zurückbleibt. Handzittern
ist ein paar Pixel groß und wird geschluckt; eine beabsichtigte Bewegung ist viel größer als die
Leine und geht ungefiltert durch. Gemessen: rund 76–79 % weniger Zittern bei großem Pinsel.

**Die Spitze ist immer hinten**, also muss `endStroke()` sie bis zum letzten echten Zeigerpunkt
nachziehen **und danach neu zeichnen** — sonst endet jeder Strich zu früh und das letzte Stück
poppt 260 ms später nach.

---

## 3g. Die Platte und die zweite Metrik

**Die Platte** setzt das erkannte Motiv mittig auf einen rechteckigen Sticker, statt der eigenen
Kontur zu folgen. Sie steht im Rand-Block über den Reglern, aber **nur nach einer Erkennung** —
in den anderen Modi gibt es kein Motiv, das man mittig setzen könnte.

**Sie ist der Körper, nicht die Freistellung.** Der erste Versuch legte sie in `alphaC()` — und
druckte das ganze Foto, weil `faceC()` mit `alphaC` das Bild beschneidet. Die Trennung, die
gelten muss:

- `alphaC(k)` = **der Umriss des Motivs**. Nur das gedruckte Bild liest sie.
- `plateC(k)` = **der Sticker-Körper** als Rechteck um den Motivumriss.
- `bodyC(k)` = das eine oder das andere. **Alles, was die Form des Stickers betrifft, liest
  `bodyC`**: das Distanzfeld, die Silhouette, der Glitter.

Weil die Platte damit in derselben Ebene liegt wie der Rand, können Milchigkeit und Schmutzkante
gar keinen abweichenden Alphaton haben — das war der Grund, sie nicht in den Rand zu legen.

**Zwei Geometriefallen:** Ein Eckbogen schneidet die Ecke um `r·(1−1/√2)` ein, also muss die
Platte um genau diesen Betrag weiter außen stehen — sonst frisst die Rundung bei Regler 0 die
Ecken des Motivs. Und **eckig heißt eckig**: bei `borderCorner === 'edge'` ist der Radius 0 und
der Regler verschwindet.

**Die zweite Metrik.** Der euklidische Abstand rundet Ecken zwangsläufig — ein eckiger Sticker
bekam runde Randecken. `edtField(k, box)` rechnet deshalb wahlweise in **Schachbrett-Metrik**
(`boxRun`): jeder der acht Nachbarn ist ein Schritt, zwei Rasterdurchläufe, exakt, und mitrierte
Ecken fallen aus der Metrik heraus. Die beiden Felder liegen in getrennten Speichern
(`_edt` / `_edtBox`).

**Die Falle dabei:** `warmField()` prüfte `_edt`, füllte aber je nach Eckenwahl `_edtBox`. Nach
einem Wechsel sah der Wächter das alte Feld, stieg aus, und die neue Metrik landete synchron im
Zeichnen — genau der Aussetzer, gegen den der Vorbau gebaut ist. **Der Wächter muss den Speicher
prüfen, der auch gefüllt wird**, und beim Umschalten wird der nicht mehr benutzte verworfen.

---

## 3h. Der WebP-Export

Die Formatreihe **PNG | WebP** steht über der Größenreihe, weil es zwei Zwecke sind und nicht
zwei Größen derselben Sache. WebP ist dabei **nicht** auf Messenger beschränkt — es ist bei
gleicher Qualität rund 25–35 % kleiner als PNG und bekommt deshalb dieselbe Reihe
(50 % · Original · 200 %). **Zusätzlich** erscheinen unter WebP zwei Voreinstellungen, jede mit
ihrem Erklärtext, weil beide sonst unverständlich sind:

- **512 × 512** — die Vorgaben von WhatsApp und Telegram. PNG wird dort gar nicht angenommen.
- **96 × 96** — das Titelbild eines ganzen Sticker-*Pakets* in der Auswahlleiste, nicht einer
  der Sticker.

**Eine feste Größe skaliert nicht, sie passt ein.** `fitBox(box)` setzt den fertigen Sticker
mittig in ein Quadrat, mit ~1,6 % Rand (bei 512 also ~8 px), damit er in der Sprechblase nicht
an der Kante klebt. Der Zwischenschritt wird in einer Auflösung gerechnet, die das Quadrat
abdeckt, und erst dann eingepasst — direkt in Zielgröße zu komponieren würde die Silhouette bei
96 px unbrauchbar grob machen.

**Die Qualität wird gestuft, nicht geraten** (`encode`): 0,92 abwärts bis die Datei unter der
Grenze liegt (100 KB bei 512, 50 KB beim Paketsymbol). Ein 512²-Bild zu kodieren ist billig, also
kosten ein paar Versuche nichts — eine feste Qualität für jedes Motiv zu wählen geht dagegen
zwangsläufig für manche Motive schief.

**Zwei Stolperstellen:**

1. **Ein Format- oder Größenwechsel muss die gehaltene Teilen-Datei verwerfen** (der Zwei-Tipp-Weg
   aus Abschnitt 8), sonst verschickt der zweite Tipp die vorige Datei unter neuer Beschriftung.
   Die Zurücknahme muss den PNG-Rückfall **umschließen**, nicht davor stehen — sonst greift sie
   genau beim Wechsel von 512 auf PNG nicht.
2. **PNG kennt die beiden Voreinstellungen nicht.** Ein Klick auf PNG bei aktivem 512/96 fällt
   auf „Original" zurück, sonst bliebe eine Kombination stehen, die es nicht gibt.

---

## 3d. Was den Rahmen betrifft und was nicht

Der Rand ist eine eigene Ebene, kein Teil des Motivs. Drei Regeln halten das auseinander:

- **Die Textstanzung und die Abnutzung greifen nur ins Motiv**, nie in den Rand. Beide arbeiten
  auf einer Kopie, die vorher mit `destination-in` auf die Motivfläche beschnitten wird.
- **Ein Loch in der Folie ist nicht Nichts.** Wo die Stanzung oder die Abnutzung durchbricht,
  liegt bei transparentem Rand dieselbe Milchigkeit wie im Rand selbst — aus derselben Quelle
  (`silC`) und mit derselben Formel (`clearMilky/100 · 0.45`). Fehlt das, sieht das Loch wie
  ein Fehler aus statt wie durchscheinende Folie.
- **Die Schmutzkante bleibt außen.** Sie ist Dreck an der Außenkante, wo der Sticker von der
  Klebefläche absteht — sie gehört nicht in ein Loch in der Mitte. Die Bänder sind deshalb nach
  außen gewichtet: die Kontur trägt volle Dichte, die breiten Bänder dahinter nur Schleier.
- **Der Holo-Effekt betrifft dagegen alles**, Rand eingeschlossen. Er liegt als Folie über dem
  ganzen Sticker, nicht im Druck. Nur die Kratzer bleiben ausgespart — Abnutzung glänzt nicht.

Beim Text hängt es am Schalter: **„Altert mit"** heißt unter dem Schmutz, **„Liegt oben"** heißt
darüber und damit vom Dreck unberührt.

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
5. **Der Aufbau des Distanzfelds** kostet nach jeder Änderung der Freistellung einmalig etwa
   eine Zehntelsekunde in Arbeitsauflösung; beim Export in Originalauflösung entsprechend mehr.
   Randbreite ziehen ist danach nur noch eine Schwellwertänderung und läuft flüssig.
6. **Holo bei Dichte 500 % + großen Splittern** rechnet bis 12 000 Formen — kurzes Stocken möglich.
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

## 5b. Der Abrieb-Befund (offen, mit Auftrag)

Der Nutzer fand in **keiner** Einstellung des Abriebs ein authentisches Ergebnis. Zur Prüfung
wurden sechs Varianten desselben Stickers gerendert (`beispiele/01-06-wear.png`, aufbereitet in
`Abrieb - Beispiele.dc.html`) — der Befund bestätigt ihn. Vier Gründe, in der Reihenfolge ihres
Gewichts:

1. **Der Abrieb sitzt an zufälligen Stellen der Fläche.** Ein benutzter Sticker reibt sich dort
   ab, wo er scheuert: Ecken zuerst, dann Kanten, die Mitte zuletzt.
2. **Die Flecken sind weiche Wolken mit runden Rändern.** Abgeplatzte Farbschicht hat harte,
   ausgefranste Kanten — sie bricht weg, sie verläuft nicht. Bei hoher Stärke liest sich das als
   Farbspritzer oder Stockflecken.
3. **Es gibt keine Richtung.** Scheuern passiert entlang einer Bewegung, echte Spuren liegen in Bahnen.
4. **Zwischen 22 % und 58 % passiert visuell wenig, dann kippt es** — der Regler ist nicht treffbar.

**Der vorgeschlagene Umbau** (vom Nutzer noch nicht beauftragt, sein Gegenvorschlag steht aus):
den Abrieb an dasselbe Distanzfeld hängen, das die Schmutzkante schon benutzt — Wahrscheinlichkeit
hoch am Rand, zur Mitte hin abfallend —, die harten Kanten und die Hüllkurve aus 3e übernehmen und
eine Vorzugsrichtung für Scheuerbahnen ergänzen. Kein neuer Effekt, sondern der Bauplan, der bei
der Schmutzkante funktioniert hat. **Nicht ungefragt umsetzen.**

---

## 6. Offene Angebote an den Nutzer

**Aus der Rückmeldung des CD Case Creators (Runde 2/3) bewusst NICHT übernommen** — jeweils mit
Grund, damit eine spätere Sitzung sie nicht als vergessen behandelt:

- *Statuszeile als verschwindende Quittung*: hier ein fester Fuß, der keinen Platz kostet — und
  zwei unserer Meldungen sind Warnungen („kein Alphakanal", „kein Motiv"), die stehen bleiben müssen.
- *Ebenen sichtbar einpassen*: kein Gegenstück, unsere Masken decken immer die ganze Fläche.
- *Messfalle `getBoundingClientRect`*: hier kein Fehler — `ptToCanvas` **muss** die Skalierung
  mitrechnen, genau darauf beruht der Zoom.
- *Pfade in JS-Zeichenketten einbetten*: wir laden kein Bild aus dem Code.
- *Zugeklappte Abschnitte*: nichts vorhanden, was die App schon richtig macht und der Nutzer
  nie anfassen soll.

**Für die mobile Runde vorgemerkt** (Punkte 2, 3, 4, 6, 12 der Rückmeldung): unteres Panel,
Orientierung per Messung statt Media Query, schwebende Kapseln über der Arbeitsfläche,
44-Punkt-Trefferflächen samt ihrer zwei Fallen (Selektor-Geltungsbereich, Container mit fester
Höhe), und der `guard()`-Umschlag um alle `on*`-Methoden plus Fehlerfenster im Panel. Einzeln
ergeben sie wenig — 44-Punkt-Knöpfe ohne mobile Anordnung machen die Desktop-Oberfläche nur klobiger.

**Als Nächstes vom Nutzer angesetzt:** eine **Fußzeile unter der ganzen App** (nicht in der
Bühne — dort wurde der Hinweis testweise eingebaut und wieder verworfen) mit dem Logo des Nutzers,
„© Patrick Majewski" und dem Hinweis auf *Teilen → Zum Home-Bildschirm* **in der Akzentfarbe**.
Das Logo arbeitet der Nutzer selbst aus und liefert es. Danach folgt eine weitere Liste an
Punkten. Erst dann lohnt der Abrieb-Umbau aus 5b.

Weiterhin offen:

- Echte PWA (Manifest, Icon, Offline-Cache), damit „Zum Home-Bildschirm" sich wie eine App verhält.
- Pinch-Zoom und Zwei-Finger-Pan auf Touch.
- Bezier-Kurven im Pfad-Werkzeug.
- Mehrere Textebenen.
- Frei wählbare Konturfarbe im Text (statt der Automatik).
- Ziehgriffe im Zuschnitt statt der Breite/Höhe-Regler.
- Speicherumschlag beim Zusammensetzen entschärfen — aufgeschoben, Befund in 6b.

## 5a. Zwei Stellen, an denen ein neuer Wert eingetragen sein muss

In dieser Runde sind zwei Fehler derselben Art passiert, beide beim Nachrüsten eines Werts:

1. **`syncUI()` liest `renderVals()` nicht.** Es baut eine eigene `derived`-Tabelle
   (oben in `syncUI`) und löst `data-*`-Attribute über
   `const val = k => (k in derived ? derived[k] : S[k])` auf. Ein Wert, der nur in
   `renderVals()` steht, ist für `data-show`/`data-dim`/`data-out` **nicht sichtbar** —
   er wird stillschweigend `undefined` und das Element bleibt dauerhaft im Aus-Zustand.
   Ein Wert, der direkt im `state` steht, geht durch (Rückfall auf `S[k]`); ein abgeleiteter
   nicht. **Abgeleitete Werte für die Oberfläche gehören in `derived`, nicht in `renderVals`.**
2. **Die Rückgabe von `renderVals()` ist in Blöcke geteilt.** Der obere benutzt die Abkürzung
   `S`, der Behandler-Block darunter nicht — dort steht überall `this.`. Wer im falschen
   Block `S` schreibt, bekommt `S is not defined` erst beim nächsten Neuzeichnen zu sehen.

Faustregel: Ein neuer Zustandswert braucht bis zu drei Einträge — `state` (Standardwert),
`derived` (wenn die Oberfläche ihn ableitet) und `renderVals` (nur wenn das Template ihn als
`{{ hole }}` oder Behandler braucht).

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

---

## 8. Der mobile Umbau (v1.3)

Eine Datei für Telefon und Rechner. Ausgewertet wurde die fertige Standalone des Schwester-
projekts **CD Case Creator**, das denselben Weg schon gegangen ist; die Muster unten stammen
von dort und wurden bewusst gleich gehalten, damit sich beide Apps identisch anfühlen.

### Die drei Lagen

`LAYOUTS` kennt `wide` (Panel rechts, Kopfleiste), `land` (Panel rechts, schmaler) und `tall`
(Panel unten, Ziehharmonika). Gewählt wird in `measureLayout()` aus der **gemessenen** Fenster-
größe, nicht aus einer Media Query — eine Media Query fragt das Gerät, wir brauchen aber die
Fläche, die die Komponente tatsächlich bekommt.

Zwei Regeln, beide waren ein Fehler bevor sie standen:

- **Die Zeile darf nie umbrechen** (`flexWrap: nowrap`). Mit Umbruch entstand ein vierter,
  unbeabsichtigter Zustand: das Panel rutschte unter die Bühne, behielt aber seine
  `max-width` — es sah aus wie das Telefon-Layout, nutzte aber die halbe Breite.
- **Die Desktop-Schwelle ist 900px, nicht 820.** Darunter passen Bühne (Basis 520) und Panel
  (360) nicht nebeneinander, wie breit sich das Fenster auch nennt.

Im `tall`-Layout ist der Panel-Titel zugleich der Auf-/Zuklapper — aber **nur wenn der Block
schon markiert ist**. Ohne diese zusätzliche Bedingung faltet der erste Tipp auf einen offenen,
unmarkierten Block ihn weg statt ihn zu wählen; beim Start trifft das garantiert Block 01.
Der Startzustand muss mit sich selbst übereinstimmen: markiert ist der Block, der offen ist.

### Die vier Ecken-Kapseln

Oben links Zoom, oben rechts Dunkelmodus und Sprache, unten links Vorschau-Hintergrund, unten
rechts Werkzeuge ein/aus. Positioniert in `placeCaps()`, das die Scrollbalkenbreite der Bühne
mitrechnet.

Ein Finger auf der Arbeitsfläche blendet sie aus (`[data-float][data-busy] { opacity: 0 }`,
0,16 s) — **außer** dem Werkzeuge-Schalter, der der Weg zurück ist.

### Navigation

Die Bühne hält `touch-action: none`, damit jede Berührung dem Werkzeug gehört. Geschoben wird
über vier Randstreifen und zwei Fingerbalken (8px sichtbar auf 40px Zielfläche, rechts und
unten). **Deren Abstände werden aus den Kapselboxen abgeleitet, die `placeCaps()` ohnehin misst
— nie aus geratenen Konstanten.** Die Kapseln stehen später im DOM bei gleichem `z-index` und
fressen jede Überlappung stumm als Trefferfläche auf; zweimal geraten, zweimal daneben.

### Zwei Finger

`gestureMode()` liest **`focus`, nicht `activeTool()`**. Das ist der Unterschied zwischen zwei
verschiedenen Fragen: `activeTool()` sagt, welche Ebene **ein** Finger verschiebt, und ist wahr,
sobald der Used Look eingeschaltet ist. Als Gestenregel wiederverwendet nahm sie damit jedem
anderen Block das Zoomen weg. Richtig ist: zwei Finger transformieren den **markierten** Block
(Used Look, Text), überall sonst navigieren sie.

Drei Fallen:

- **Der erste Finger verliert seinen Zug**, wenn der zweite aufsetzt — ein Kneifen ist nicht der
  Schwanz einer Verschiebung, sonst bekommt man bei jeder Geste einen Kratzer geschenkt.
- **Das Anheben eines von zwei Fingern beendet die Geste**, statt den verbliebenen zu einer neuen
  Verschiebung zu machen.
- **Aufgeräumt wird am Fenster, nicht auf der Bühne.** Nur der erste Finger bekommt eine
  Zeigerbindung; wird der zweite über dem Panel losgelassen, erreicht die Bühne kein Ereignis,
  sein Eintrag überlebt, und die nächste Einzelberührung zählt als zwei — die Ebene schlug dann
  sofort an den Anschlag.

**Geglättet** wird über einen einpoligen Tiefpass (~65 ms) auf Faktor, Winkel und Mittelpunkt.
Der Winkel wird als kürzester Bogen aufaddiert, nie roh geglättet — sonst springt er beim
Überschreiten von ±180° einen ganzen Kreis. Totzonen von 2 % und 3° trennen reines Kneifen von
reinem Drehen. Ohne all das zittert die Geste am Bildrand sichtbar, weil iOS dort ungenauer
meldet und der Kneiffaktor ein Verhältnis zweier naher Punkte ist.

### Kapseln, Balken und Regler (dritte Runde)

- **Eine Kapselgruppe misst man nach dem Zeichnen, nicht davor.** `placeCaps()` lief im selben
  Zug wie die Zustandsänderung und las die Kapselbreite von *vorher*; der Fingerbalken begann
  dann unter der Kapsel und fraß ihre Trefferfläche. Es gibt jetzt `placeCaps()` (ein
  `requestAnimationFrame`) und `placeCapsNow()` (die Messung).
- **Der waagerechte Balken sitzt an der Unterkante und weicht seitlich aus**, statt die Kapseln
  anzuheben: er beginnt rechts der Hintergrundgruppe und endet links vom Werkzeuge-Schalter.
- **Ein Tippen auf die Reglerschiene darf nichts setzen.** Ein `input[type=range]` springt zum
  Klickpunkt — auf dem Telefon verstellt das ständig etwas. `onSliderGrab` verwirft jeden Druck,
  der weiter als 30 px vom Griff entfernt liegt. **Nicht** über `pointer-events` auf dem Griff
  lösen: das schrumpft die Trefferfläche auf 18 px, also das Gegenteil der Absicht.
- **Ein neuer Behandler braucht seinen Eintrag in `renderVals()`** — fehlt er, bleibt das
  `{{ }}`-Loch unaufgelöst und der Knopf ist stumm, ohne Fehlermeldung. (Siehe auch 5a.)
- **Gleiche Sache, gleiche Darstellung:** die Vorschau-Hintergründe sind am Rechner wie am
  Telefon drei Farbflächen auf einer Höhe von 34 px — vorher Wörter oben, Flächen unten.

### Was iOS sonst verlangte

- `-webkit-touch-callout: none` plus unterdrücktes `contextmenu` innerhalb der App, sonst geht
  beim Halten das Teilen-Blatt auf.
- **Textmarkierung nur für Textfelder**, nicht für alle `input`. Ein markierbarer Regler holt
  die Auswahl-Lupe auf ein Element, das man nur ziehen können soll.
- **Regler 40px hoch mit 18×30-Griff und `touch-action: pan-y`.** Die Schiene bleibt optisch
  2px. Ohne `pan-y` wartet iOS erst ab, ob eine Bewegung ein Seitenscrollen wird, und schluckt
  die ersten Millimeter — das ist das Hakelige.
- **`pointerleave` gehört nicht ans Ausblenden.** Es beendete den Strich, sobald der Finger den
  Bühnenrand kreuzte, während die Zeigerbindung unsichtbar weiterzeichnete. Das Ausblenden endet
  jetzt auf einem Loslassen am Fenster.
- `overscroll-behavior` auf Seite und Panel, damit nichts gummibandartig an die Seite durchreicht.
- **Die Seite selbst darf nicht zoomen — dafür braucht es drei Dinge, jedes einzeln unzureichend:**
  `touch-action: pan-x pan-y` auf `html, body` (`manipulation` nimmt nur den Doppeltipp weg,
  Kneifen bleibt erlaubt; `none` verbietet sich, weil sich `touch-action` die Vorfahrenkette
  hinunter verschneidet und dem Panel sein `pan-y` nähme), `gestureend` zusätzlich zu
  `gesturestart`/`gesturechange` (Safari wendet den aufgelaufenen Maßstab auch dort an), und ein
  abgewiesenes `touchmove` bei mehr als einem Finger innerhalb der App — das ist das einzige
  Ereignis, das sich noch abweisen lässt, wenn ein Zoom bereits begonnen hat. Der Fehler trat
  selten auf, weil nur der dritte Weg offenbleibt, und nur wenn die Berührung außerhalb der
  Arbeitsfläche beginnt.

### Rechenlast

Der Hauptthread ist auf dem Telefon der Engpass, nicht die Grafik:

- **Regler sind gedrosselt.** Solange einer gehalten wird, höchstens alle 90 ms neu rechnen; beim
  Loslassen immer ein letzter voller Durchgang, damit der abgesetzte Wert der gezeigte ist.
- **Das Distanzfeld wird vorgebaut** (`warmField()`, 350 ms nachdem die Freistellung ruhig ist),
  nie während eines Strichs oder Reglerzugs. Beim Einschalten des Rands ist es dann schon da.
- **Float32 statt Float64**, Saat direkt aus der Freistellung statt über eine gepolsterte Kopie,
  Wurzel an Ort und Stelle, und ein 32-Bit-Speicherzugriff statt vier beim Aufbau der Silhouette.
  Zusammen rund ein Drittel des vorherigen Speicherverkehrs.

### Export

`navigator.share` legt das PNG auf dem Telefon in Fotos statt in Dateien — **nur über https**,
außerhalb eines sicheren Kontexts ist `canShare` gar nicht vorhanden. Dazu ein zweiter Weg: das
PNG-Kodieren dauert länger als die Tippgeste, iOS öffnet das Blatt dann nicht mehr
(`NotAllowedError`). Statt stumm auf Download zu fallen hält die App die fertige Datei und
beschriftet den Knopf um — der zweite Tipp ist eine frische Geste und geht durch.

**Die Exportgrößen heißen 50 % · Original · 200 %**, alle relativ zur Ausgangsdatei, mit der
tatsächlichen Pixelgröße darunter. Die alten `1×/2×/Original` waren relativ zur internen
Arbeitsauflösung und damit für niemanden nachvollziehbar. Anzeige, Statuszeile und Deckel messen
alle die **gepolsterte** Kante, sonst nennt die App zwei verschiedene Größen für dieselbe Datei.

---

## 8a. Die Bedienlogik des Panels

Sechs gleichartige Blöcke, **01 und 06 eingeschlossen** — Export war zuletzt der einzige Sonderfall
(nur auf-/zuklappbar, ohne Rahmen und Markierung) und ist jetzt auf demselben Weg gebaut:
`[data-tool="k"]`-Umschlag, `[data-tool-pick="k"]`-Titelzeile, `select()`.

**Was auswählt und was nicht.** Die Titelzeile wählt, klappt auf und springt an den Blockanfang.
Ein Finger auf der **leeren Blockfläche** tut nichts: dort setzt man zum Scrollen an, und eine
Auswahl riss das Panel unter dem Daumen weg. Ein Regler oder Knopf **im** Block holt die Markierung
weiterhin dorthin — das Ziehen auf dem Sticker muss ihr folgen —, aber **ohne** den Sprung.

**Der Sprung endet am Panelende** (`min(top, scrollHeight − clientHeight)`). Der letzte Block
erreicht die Oberkante ohnehin nie; ihn dorthin zu zwingen schöbe seine Bedienelemente aus dem Bild.

**Aus/An sind zwei Flächen**, kein umschaltender Knopf: der Zustand steht dort, wo man drückt,
gewählt hell auf Akzent. 44px je Zelle, im Querformat 36 — dort ist das Panel auf seiner
Mindestbreite, und dann tritt die **Blocknummer** zurück, nie die Trefferfläche.

**Die Sperre** (`state.locks`) legt die Regler eines Blocks auf 45 % und nimmt ihnen die Eingabe,
das Aus/An-Paar eingeschlossen. Zwei Fallen:

1. **Die Sperre gilt der Ebene, die gezogen würde — nicht dem markierten Block.** Mit gesperrtem
   Used Look und markiertem Holo fragte die erste Fassung „ist Holo gesperrt?", fand nein und gab
   die Fläche trotzdem an Used Look. `activeTool()` behandelt eine gesperrte Ebene jetzt wie eine
   ausgeschaltete: für den Zug unsichtbar, damit die andere die Fläche bekommen kann.
2. **Ihre Optik kommt aus CSS**, nicht aus `syncUI`-Schreibzugriffen: `[data-lock][data-shut]`.
   Dasselbe Muster wie `[data-float][data-busy]`.

**Die Kapsel oben rechts klappt ein** (222 → 54px). Drei getrennte Einheiten mit je eigenem Rahmen —
Dunkelmodus solo, EN|DE als gerundetes Duo mit Trennlinie, Pfeil solo; eine gemeinsame Hülle band
das Sprachpaar optisch an das Erscheinungsbild. Der Pfeil zeigt nach rechts, wohin die Gruppe geht,
und nach links, um sie zurückzuholen. **Die Rundung des Duos steht in CSS**, weil ein Inline-Radius
an diesen zwei Zellen nicht überlebt.

---

## 9. Fehler, die nicht aus der App kommen

Ein Browser gibt Fehler aus einem Skript, das er als fremd ansieht, nur als `Script error.` heraus —
ohne Meldung, Datei und Zeile. In einer gebündelten Einzeldatei betrifft das **jeden** Fehler, und
ob er ihn so behandelt, unterscheidet sich je Browser (im Schwesterprojekt trat er in genau einem
von zwei mobilen Browsern auf). Das ist eine Sicherheitsregel, keine Diagnose — und deshalb war er
dort nie „behebbar", wie oft auch immer jemand den Quelltext durchsah.

Vier Teile, aus dem CD Case Creator übernommen:

1. **Zäune.** Jede `on*`-Methode läuft in einem `try`, angebracht in `componentDidMount` über die
   eigenen Schlüssel — eine neue Methode ist damit automatisch mit drin.
2. **Netze.** `window.onerror` und `unhandledrejection` fangen, was außerhalb passiert.
3. **Das Feld des Bundlers.** Die Einzeldatei bringt ein eigenes `#__bundler_err` mit, fest am
   unteren Rand — genau über den Werkzeugen. Eine CSS-Regel hält es unsichtbar
   (`display: none !important` schlägt sein Inline-Styling), ein `MutationObserver` räumt es beim
   Auftauchen weg und schreibt seinen Text ins eigene Protokoll. **Ein Intervall allein genügt
   nicht:** der Bundler baut das Feld bei jedem Fehler neu auf, und bis zum nächsten Durchlauf
   liegt es im Weg.
4. **Fehlerfenster im Panel** mit Zähler, Kopieren und Leeren; ein zensiertes `Script error.` wird
   dort ausdrücklich als solches gekennzeichnet, statt als echter Fehler zu lesen.

**Zur Fehlersuche selbst:** nie im selben Aufruf klicken, `setState` rufen **und** `getComputedStyle`
lesen. Das liefert eine veraltete Stilauflösung und sieht wie ein Fehler aus — auf diese Weise habe
ich der Sperre zweimal einen Defekt angedichtet, den sie nicht hatte. Verändern und messen gehören
in getrennte Schritte.
