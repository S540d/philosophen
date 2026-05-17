# Philosophische Denkschulen – Abhängigkeitsgraph

**[→ Zur Lernseite öffnen](https://s540d.github.io/philosophen/start.html)**

Ein interaktiver Lern-Graph der westlichen Philosophiegeschichte – für alle, die verstehen wollen, wie die großen Denkschulen zusammenhängen, sich beeinflussen und voneinander abgrenzen.

## Ziel

Diese Seite richtet sich an alle, die sich für Philosophie interessieren und einen schnellen, visuellen Einstieg suchen. Statt trockener Textwände zeigt ein interaktiver Graph, wie Ideen durch die Jahrhunderte fließen – wer wen beeinflusst hat, wer mit wem gebrochen hat und welche Synthesen entstanden sind.

## Inhalt

- **22 philosophische Denkschulen** von der Antike bis ins 20. Jahrhundert
- **~40 typisierte Verbindungen** zwischen den Schulen:
  - *Beeinflusst* – eine Schule prägt eine andere
  - *Setzt fort* – direkte inhaltliche Fortsetzung
  - *Kritisiert / Bricht mit* – bewusste Abgrenzung
  - *Synthetisiert* – Verschmelzung zweier Traditionen
- **Detail-Panel** pro Schule: Kernthese, Vertiefung, bedeutende Vertreter, alle Verbindungen
- **Epochen**: Antike · Mittelalter · Neuzeit · 19. Jahrhundert · 20. Jahrhundert

## Benutzung

1. `start.html` im Browser öffnen (kein Server nötig – läuft lokal)
2. Knoten hovern → Verbindungen werden hervorgehoben
3. Knoten klicken → Detailpanel öffnet sich rechts
4. Klick auf freie Fläche oder `✕` → Panel schließen

## Technologie

- **D3.js v7** – SVG-Rendering und Interaktion
- **Cormorant Garamond / Josefin Sans** – Typografie via Google Fonts
- Reines HTML/CSS/JS – keine Build-Toolchain nötig

## Lokale Entwicklung

```bash
# Datei direkt im Browser öffnen (Pfad je nach Betriebssystem anpassen):
# macOS:   open start.html
# Linux:   xdg-open start.html
# Windows: start start.html

# Oder mit einem lokalen Entwicklungsserver:
npx serve .
```

## Mitwirken

Verbesserungsvorschläge und neue Denkschulen sind willkommen – Issues und Pull Requests gerne einreichen.

## Lizenz

Siehe [LICENSE](LICENSE).
