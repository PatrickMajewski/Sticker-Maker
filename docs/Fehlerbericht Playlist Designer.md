# Zwei Dunkelmodus-Fehler im Playlist Designer v1.1.1

Gefunden beim Portieren des Dunkelmodus in den Sticker Maker. Beide stecken auch im Playlist
Designer — dort im Hellmodus unsichtbar, im Dunkelmodus wirksam. Zeilenangaben beziehen sich auf
`Spotify Playlist Designer v1.1.1.dc.html`.

---

## 1 · Die hellen Akzentstufen fehlen in der `DARK`-Map

Die Rückmeldung nennt die Regel richtig — „die tiefen Stufen (600–900) auf die hellen legen und
umgekehrt" — die Map spiegelt aber nur **300, 400, 600, 700, 800, 900**. `--color-accent-100`
und `--color-accent-200` bleiben hell (#fff2ef, #ffe0d9).

Das fällt nicht auf, solange man auf die *Schrift* schaut. Es trifft die **getönten Flächen**:
das Design-System definiert `.tag-accent` als `background: var(--color-accent-100); color:
var(--color-accent-800)`. Im Dunkelmodus wird die Schrift korrekt hell (#ffc4b8), die Fläche
bleibt aber fast weiß — gemessen **1,39:1**, praktisch unlesbar, und optisch ein greller heller
Fleck auf dem dunklen Panel.

**Betroffen hier:** `syncUI()`, Zeile 802 —
`el.className = 'tag ' + (on ? 'tag-accent' : 'tag-neutral')`. Das sind die drei ✓-Kennzeichen
neben „Maske Grün", „Maske Rot" und „Reflexion / Glanz" (Zeilen 103, 111, 119), sobald eine
Datei geladen ist. `tag-neutral` ist unauffällig, weil die Neutralrampe vollständig gespiegelt
ist — genau daran sieht man, dass es an der Rampe liegt und nicht am Tag.

Ebenso zu prüfen: jede andere Stelle, die aus `--color-accent-100/200` malt (Hinweisfelder,
getönte Karten).

**Lösung** — zwei Zeilen in die `DARK`-Map, analog zu neutral-100/200:

```js
'--color-accent-100': '#3a1c15', '--color-accent-200': '#4d241a',
```

Danach im Sticker Maker gemessen: Tag-Kontrast von 1,39:1 auf brauchbare Werte, Fläche liest
sich als dunkle Tönung statt als heller Fleck.

---

## 2 · Die Vorschau-Hintergründe kippen mit dem Thema

Zeile 819:

```js
this.holder.style.background = S.bg === 'dark' ? 'var(--color-neutral-900)'
  : (S.bg === 'light' ? 'var(--color-neutral-100)' : checker);
```

Die `DARK`-Map spiegelt beide Stufen. Im Dunkelmodus gemessen: **„Dunkel" liefert #f5f1f1
(fast weiß), „Hell" liefert #2d2b2b (fast schwarz)** — die beiden Knöpfe tun genau das
Gegenteil dessen, was draufsteht.

Die Ursache ist begrifflich, nicht technisch: das sind keine Oberflächenfarben, sondern
**Prüfgründe**, gegen die der Nutzer sein freigestelltes Motiv beurteilt. Sie dürfen dem Thema
der Oberfläche nicht folgen. Ein Nutzer, der im Dunkelmodus arbeitet, will trotzdem sehen, wie
die Hülle auf hellem Grund wirkt.

**Lösung** — feste Werte statt Tokens:

```js
if (S.bg === 'dark') h.background = '#1c1b1a';
else if (S.bg === 'light') h.background = '#f4f2f0';
else h.background = checker;
```

**Das Schachbrett bleibt wie es ist.** Es malt aus `--color-neutral-300` und
`--color-neutral-100`, also kippen beide Stufen gemeinsam — das Muster bleibt ein Muster, nur
dunkel. Das ist erwünscht: es steht für „hier ist nichts", nicht für eine bestimmte Helligkeit.
Nur die beiden benannten Grundfarben sind das Problem.

---

## Prüfvorschlag

Beides ist in einer Minute nachweisbar: Dunkelmodus einschalten, eine Maskendatei laden (das
✓-Kennzeichen erscheint), dann „Dunkel" und „Hell" durchklicken. Wer es messen will:

```js
getComputedStyle(document.querySelector('.tag-accent')).backgroundColor  // erwartet: dunkel
getComputedStyle(holderElement).backgroundColor                          // bei "Dunkel": dunkel
```

---

## Sonst nichts

Der restliche Dunkelmodus im Playlist Designer hält der Prüfung stand: Neutralrampe vollständig
gespiegelt, `#f8f4f4` konsequent auf allen Akzentfüllungen, Hin- und Zurückschalten hinterlässt
keine Variablen. Zwei Ergänzungen, die im Sticker Maker dazugekommen sind und dort vielleicht
auch lohnen:

- **`'color-scheme': 'dark'` in die `DARK`-Map** (eine gewöhnliche CSS-Eigenschaft, der
  vorhandene Loop setzt und entfernt sie mit). Ohne sie malt der Browser Scrollbalken und
  Slider-Bahnen weiterhin hell — bei einem langen Einstellungs-Panel ein deutlich sichtbarer
  heller Streifen am rechten Rand.
- **`document.body`** bleibt hell und blitzt beim Überscrollen durch; beim Umschalten
  mitfärben und in `componentWillUnmount` zurücksetzen.
