# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Projekt

Interaktiver Lern-Graph der westlichen Philosophiegeschichte als einzelne HTML-Datei (`start.html`). Kein Build-System, kein Backend, keine Abhängigkeiten außer CDN-Links.

## Entwicklung

```bash
# Lokal öffnen
open start.html          # macOS
xdg-open start.html      # Linux
start start.html         # Windows

# Oder mit Dev-Server (empfohlen für lokale Ressourcen):
npx serve .
```

Kein Build-Schritt, keine Tests, kein Linter. Änderungen sind sofort im Browser sichtbar (Reload genügt).

## Architektur: `start.html`

Die gesamte Anwendung lebt in einer einzigen Datei, aufgeteilt in drei Bereiche:

### 1. Datenschicht (JS, oben im `<script>`-Block)

- **`nodes[]`** – 22 Denkschulen, jede mit `id`, `label`, `era`, `core`, `detail`, `reps[]`
- **`edges[]`** – ~40 Verbindungen mit `s` (source-id), `t` (target-id), `type`, `label`
- **`ERA_COLORS`** – Farbzuordnung pro Epoche (wird für Knoten, Swim-Lane-Labels und Panel-Akzente verwendet)
- **`EDGE_COLORS`** – Farbzuordnung pro Verbindungstyp (`influences`, `continues`, `criticizes`, `synthesizes`)

### 2. Layout-Engine (`layoutNodes()`)

Ordnet Knoten in horizontalen Swim-Lanes nach Epoche an. Gibt `posMap` (id → {x, y, w, h}), `totalWidth` und `totalHeight` zurück. Kanten, deren `s` oder `t` nicht in `posMap` liegt, werden gefiltert (so wird der ungültige `hermeneutik`-Verweis still ignoriert).

### 3. Render & Interaktion (D3.js)

- SVG wird direkt auf `#main-svg` gezeichnet
- Kanten als `<path class="edge-path">` mit SVG-Arrowhead-Markern (`<defs>`)
- Knoten als `<g class="node-group">` mit `<rect>`, Era-Tag und Label
- **Hover**: hebt verbundene Kanten hervor (`.highlighted` / `.dimmed`)
- **Click**: befüllt `#panel-content` mit HTML aus den Node-Daten und öffnet das Panel per CSS-Klasse `.open`

## Neue Denkschulen hinzufügen

1. Objekt zu `nodes[]` hinzufügen (Felder: `id`, `label`, `era`, `core`, `detail`, `reps[]`)
2. `era` muss einem der fünf Einträge in `ERAS[]` entsprechen
3. Kanten in `edges[]` eintragen (`s`/`t` = ids, `type` = einer der vier Typen)

## Branch-Konvention

Feature-Branches nach Schema `claude/<feature-name>` – je Issue ein Branch und PR.
