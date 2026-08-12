# Sticker Maker

Aus einem Bild wird ein druckreifer Sticker als transparentes PNG — Freistellen, Rand,
Gebrauchsspuren, Holo-Finish und Text. **Läuft vollständig im Browser.** Eine einzige
HTML-Datei, kein Konto, kein Upload, kein Server: die Bilder verlassen das Gerät nicht.

---

## Was diesen Maker besonders macht

**Ein exakter Umriss, kein nachgezeichneter.** Der Rand entsteht aus einem echten Distanzfeld:
für jeden Bildpunkt wird der Abstand zum Motiv berechnet, der Rand ist die Schwelle darauf. Bei
5 px genauso rund wie bei 70 px, und die Kante ist ein Schnitt mit einem antialiasenden Pixel —
kein Verlauf, der die Form aufweicht. „Kontur glätten" rundet nur noch die Linienführung aus,
wenn du bewusst eine Die-Cut-Form statt der exakten Motivkontur willst.

**Vier Wege zur Form — die Erkennung ist optional.** Die meisten Werkzeuge zwingen jedes Bild
durch eine Motiverkennung. Hier ist sie einer von vier gleichberechtigten Wegen: vollflächig
zuschneiden, Pfad von Hand klicken, Motiv erkennen lassen, oder ein fertig freigestelltes PNG
unverändert übernehmen. Wer die Vorarbeit schon geleistet hat, braucht nur noch Rand und Effekte.

**Der transparente Rand sieht aus wie transparente Folie.** „Transparent" heißt hier nicht
„unsichtbar": *Milchig* gibt der Klarfläche den leichten Schleier echter Folie.

**Die Schmutzkante ist abgeplatzte Kante, kein Schleier.** Kein weichgezeichnetes Band nach
innen, sondern unregelmäßige Ausbrüche direkt auf der Kontur: die meisten Stellen bleiben fast
unberührt, einzelne Strecken sind tief hineingefressen, die Innenkante zerfranst in Krümel. Die
**Tiefe ist ein eigener Regler** und hängt nicht an der Randbreite — der Dreck darf bewusst auf
das Motiv laufen.

**Die Gebrauchsspuren folgen der Randwahl.** Weißer Rand → weiße Kratzer. Transparenter Rand →
echte Löcher durch die Folie. Kein aufgemalter Filter, sondern dieselbe physikalische Logik wie
beim echten Aufkleber.

**Ein Regenbogenfeld für alles.** Der Pastellverlauf und die Farbe jedes einzelnen Splitters
bzw. Glitterkorns stammen aus **demselben** Farbfeld — Nachbarn teilen den Farbton, ein Sweep
läuft sichtbar über den Sticker. Zwei unabhängige Zufallseffekte nebeneinander sehen falsch aus;
das hier nicht.

**Glanz liegt nicht auf Kratzstellen.** Eine Kratzstelle ist abgetragene Folie — dort ist der
Glanz weg. Und beim Glitter tragen Regenbogen und farbige Körnung nur den Druck, während der
weiße Die-Cut-Rand dieselbe Körnung klar und entsättigt trägt: Klarglitter über der Folie.

**Text, der mitaltert.** Die Schrift liegt standardmäßig **unter** Gebrauchsspuren und Glitter,
wird also mit dem Sticker abgenutzt — sie ist Teil des Drucks, kein Overlay. Für Schlagzeilen
gibt es den Umschalter „Obendrauf".

**Zehn Schriften eingebettet.** Keine Google-Fonts-Abfrage, kein Netz nötig, alle unter SIL Open
Font License oder Apache 2.0 — mit den Ergebnissen darf verkauft werden.

**Zoom, der die Werkzeuge nicht bricht.** Bei jeder Zoomstufe treffen Pinsel, Pfadpunkte und
Zuschnittrahmen exakt dort, wo geklickt wird.

**Ein stabilisierter Pinsel.** Die Spitze läuft dem Zeiger hinterher statt auf ihm zu sitzen:
Handzittern wird herausgemittelt, eine gewollte Bewegung geht ungefiltert durch. Mit Maus wie
mit dem Finger.

**Dieselbe Datei auf Telefon und Rechner.** Hochformat, Querformat, Desktop — kein zweiter
Build, keine App-Version. Auf dem Telefon: ein Finger arbeitet, zwei Finger zoomen und
verschieben; im markierten Block skalieren und drehen sie stattdessen die Ebene. Die
Bedienelemente sitzen als Kapseln in den Ecken der Arbeitsfläche und blenden aus, solange ein
Finger auf dem Sticker liegt.

**Zweisprachig und mit Dunkelmodus.** Deutsch/Englisch auf Knopfdruck, helles und dunkles
Erscheinungsbild.

---

## Sofort ausprobieren

**Lokal.** [`Sticker Maker.html`](Sticker%20Maker.html) herunterladen und doppelklicken.
Das war's — alles außer der KI-Erkennung funktioniert ohne Internet.

**Online.** Dieselbe Datei auf beliebigen Webspace legen, oder im Repository unter
*Settings → Pages* GitHub Pages aktivieren — `index.html` ist dieselbe App und wird dann direkt
ausgeliefert. Danach von jedem Gerät per Link erreichbar.

**Als App.** Im Browser geöffnet: Chrome/Edge → „Diese Seite als App installieren".
Auf dem Telefon: Teilen → „Zum Home-Bildschirm". Eigenes Symbol, eigenes Fenster, kein
Store-Konto. (Fürs iPhone muss die Seite über eine `https`-Adresse laufen.)

---

## Schritt für Schritt

### 1 · Bild laden

Ins Vorschaufenster ziehen, anklicken zum Auswählen, oder mit `Cmd/Strg + V` aus der
Zwischenablage einfügen.

### 2 · Form festlegen — vier Wege

Der Weg wird **vor** dem Laden gewählt, damit kein Bild ungewollt durch die Erkennung läuft.

| Weg | wofür | wie |
| --- | --- | --- |
| **Vollflächig** | Fotos, Memes, alles Rechteckige | Format wählen (frei · 1:1 · 4:5 · 3:4 · 16:9 · 9:16), Rahmen ziehen, *Zuschnitt übernehmen* → **Eckig** oder **Gerundet** mit Radius bis 100 % (= Pille bzw. Kreis) |
| **Pfad** | schwierige Motive, volle Kontrolle | Punkte klicken, gerade Linien dazwischen, Form schließt selbst. Punkt ziehen justiert, Klick auf eine Linie setzt einen Punkt ein, *Auswahl übernehmen* |
| **Motiv erkennen** | Personen, Produkte, Tiere | Freistellung durch ein KI-Modell im Browser, danach Kantenweichheit und Kanteneinzug feinjustieren |

Bei den drei Wegen ohne Erkennung steht die Kantenweichheit auf 0 — eine gezeichnete oder
mitgelieferte Kontur ist exakte Geometrie und braucht keine Glättung. Nur die Erkennung bekommt
sie, weil ihre Maske rauscht.
| **Freigestellt** | fertige PNGs mit Transparenz | der Alphakanal der Datei wird unverändert übernommen — keine Erkennung, kein Zuschnitt |

Bei „Motiv erkennen" und „Freigestellt" gibt es zusätzlich die **Feinkorrektur**: direkt im
Vorschaubild radieren oder zurückholen, Pinselgröße 2–200 px. Während du korrigierst, liegt die
**Originalgrafik blass im Hintergrund** — du siehst, wo das Motiv wirklich endet, statt zu raten.
**Zurück** nimmt den letzten Strich weg, zehn Schritte weit. Der Knopf zeigt an, wie viele noch
übrig sind.

### 3 · Rand

An/aus, dann **Weiß** (klassischer Sticker-Rand) oder **Transparent** (Klarfolie).
Randbreite 0–70 px, *Kontur glätten* 0–100 % für einen Die-Cut-Umriss statt der exakten
Motivkontur. Bei transparentem Rand kommt **Milchig** dazu. **Schmutzkante** (Stärke und Tiefe)
gibt es in beiden Modi.

### 4 · Used Look

Kratzer, Abrieb oder Knicke — prozedural erzeugt, oder eine eigene Maske als Bild hochladen
(mit Umschalter, ob Weiß oder Schwarz die Kratzstelle ist). Stärke, Maskengröße 0,2–3×,
Drehung, horizontal spiegeln, zentrieren, **neu würfeln** — und Ziehen im Vorschaubild
verschiebt die Maske.

### 5 · Holo-Effekt

**Regenbogen** legt einen breiten Pastellverlauf über die Fläche. Darauf wahlweise:

- **Splitter** — kantige Broken-Glass-Fragmente, jedes in einer flachen Farbe
- **Glitter** — feine Körnung mit Eigenvariation, einzelne Körner blitzen fast weiß auf

Dazu Größe, **Dichte bis 500 %** und *Zufällige Reflexion*, die das ganze Farbfeld neu würfelt.

### 6 · Text

Zehn Schriften als Raster, jede in ihrem eigenen Schnitt gesetzt — oder eine eigene
Schriftdatei laden. Farbe als **Weiß · Schwarz · Farbe · Stanzen**; „Farbe" öffnet Farbton,
Sättigung und Helligkeit, „Stanzen" schneidet den Text durch den Sticker hindurch.
Die Kontur wählt ihre Farbe selbst: Schwarz hinter weißer Schrift, Weiß hinter schwarzer, und
bei einer Farbe derselbe Ton eine Spur lauter — nie die stumpfe Gegenfarbe.
Größe, Drehung, Laufweite, Deckkraft; Ziehen positioniert. Umschalter **Mit altern / Obendrauf**.

### 7 · Export

Erst das **Format**, dann die **Größe**.

**PNG** oder **WebP**, beide in 50 % · Original · 200 %, jeweils bezogen auf deine Ausgangsdatei —
die tatsächliche Pixelgröße steht darunter. WebP ist bei gleicher Qualität rund ein Drittel
kleiner als PNG und kann ebenfalls transparent sein.

Unter WebP kommen zwei Voreinstellungen dazu: **512 × 512** erfüllt die Vorgaben von WhatsApp und
Telegram (Transparenz, unter 100 KB, schmaler freier Rand — PNG wird dort nicht angenommen), und
**96 × 96** ist das Titelbild eines ganzen Sticker-Pakets. Der Sticker wird dabei mittig in das
Quadrat eingepasst, nicht beschnitten, und die Qualität so weit gesenkt, bis die Datei unter der
Grenze liegt. Auf dem Telefon geht der Sticker über das
Teilen-Menü direkt nach Fotos (dafür muss die Seite über `https` laufen), am Rechner als
Download.

---

## Werkzeuge rund um die Bühne

| | |
| --- | --- |
| **Zoom** | −/Wert/+ · Klick auf den Wert passt ein · `Strg/Cmd` + Scrollen zoomt auf den Zeiger · mittlere Maustaste verschiebt |
| **Vorschau-Hintergrund** | Schachbrett · Dunkel · Hell · eigenes Foto (zeigt den Sticker auf Laptop, Flasche, Notizbuch) |
| **Sprache** | Deutsch / Englisch |
| **Erscheinungsbild** | Hell / Dunkel |

---

## Ohne Internet

Alles außer der KI-Erkennung: Zuschnitt, Formwahl, Pfad, PNG-Alpha, Pinsel, Rand, Used Look,
Holo, Glitter, Text und Export. Die Schriften sind in der Datei eingebettet.

Die **KI-Erkennung** lädt ihr Modell beim ersten Mal einmalig aus dem Netz (~45 MB, danach aus
dem Browser-Cache). Ist kein Netz da, springt automatisch eine Offline-Freistellung ein, die
sich mit dem Pinsel nachbessern lässt.

---

## Lizenzen

**Der Code** steht unter MIT — siehe `LICENSE`.

**Die zehn Schriften** stehen unter SIL Open Font License oder Apache 2.0: mit den Ergebnissen
darf verkauft werden, nur die Schriftdatei selbst nicht weiterverkauft.

**Das Erkennungsmodell** ([RMBG 1.4](https://huggingface.co/briaai/RMBG-1.4) von BRIA AI) ist
**nur für nicht-kommerzielle Nutzung** frei; für kommerzielle Nutzung braucht es eine
Vereinbarung mit BRIA. Das betrifft ausschließlich den Weg „Motiv erkennen" — vollflächig, Pfad
und Freigestellt nutzen kein Modell und sind davon unberührt. Dies ist keine Rechtsberatung.

---

## Technik in Kürze

Reines HTML, CSS und JavaScript, kein Build-Schritt, keine Abhängigkeiten zur Laufzeit außer
dem KI-Modell. Die Bildverarbeitung läuft auf 2D-Canvas.

Der Kern ist **maskengetrieben**: alles hängt an einer einzigen Graustufen-Maske. Woher sie
kommt — Erkennung, Pfad, Rechteck, PNG-Alpha oder Pinsel — ist allem Nachgelagerten egal. Rand,
Gebrauchsspuren, Holo, Text und Export lesen nur diese Maske. Eine neue Formquelle liefert eine
Maske und fasst die Pipeline nicht an.

Ausführlich in [`docs/HANDOFF.md`](docs/HANDOFF.md): Architektur, sämtliche Entscheidungen mit
Begründung, bekannte Grenzen und der Weg zu einer mobilen Umsetzung.

---

## Was liegt wo

| Datei | Inhalt |
| --- | --- |
| `Sticker Maker.html` | **die App** — eine Datei, alles enthalten, läuft per Doppelklick |
| `index.html` | identische Kopie, damit GitHub Pages die App direkt ausliefert |
| `src/Sticker Maker.dc.html` | der Quelltext, aus dem die Einzeldatei gebaut wird |
| `src/fonts.css` | die zehn Schriften als eingebettete `data:`-WOFF2 |
| `docs/HANDOFF.md` | vollständiges Protokoll: Quellen, Architektur, Entscheidungstabelle, bekannte Grenzen, Weg zur mobilen App |
| `docs/Projektgedaechtnis.md` | Kurzfassung der verbindlichen Konventionen (im Arbeitsprojekt als `CLAUDE.md`) |
| `docs/Fehlerbericht Playlist Designer.md` | zwei Dunkelmodus-Fehler in der Partner-App, mit Zeilenangaben und Lösung |
| `LICENSE` | MIT für den Code, plus die Hinweise zu Schriften und Modell |

**Vor dem ersten Push:** in `LICENSE` `<DEIN NAME>` ersetzen.

`Sticker Maker.html` und `index.html` sind bewusst identisch und werden gemeinsam aus
`src/` neu gebaut — wer eine ändert, muss beide ersetzen.

---

## English

Turns any image into a print-ready sticker as a transparent PNG — cut-out, border, wear, holo
finish and text. **Runs entirely in your browser**, as a single HTML file: no account, no
upload, no server, images never leave the device. The interface switches between German and
English, and has a dark mode.

Four independent ways to get a shape: crop a full-bleed rectangle, click a path by hand, let an
in-browser AI model detect the subject, or take the alpha channel of an already cut-out PNG
as-is. The transparent border behaves like real clear vinyl (milky haze, grime collecting at the
outer edge), wear follows the border choice (white scratches vs. real holes), rainbow wash and
every single flake or glitter grain take their colour from one shared colour field, gloss never
sits on a scratch, and text ages along with the sticker unless you flip it on top.

Download `Sticker Maker.html` and open it — everything except AI detection works offline.
Model licence note above applies: RMBG 1.4 is non-commercial only; the other three modes use no
model at all. Full engineering notes, decision log and known limits are in
[`docs/HANDOFF.md`](docs/HANDOFF.md) (German).
