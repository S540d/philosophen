# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Projekt

Interaktiver Lern-Graph der westlichen Philosophiegeschichte als einzelne HTML-Datei (`start.html`). Kein Build-System, kein Backend, keine Abhängigkeiten außer CDN-Links.

**Live-Seite**: https://s540d.github.io/philosophen/start.html

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

- **`PATHS[]`** – 4 Lernpfade mit `id`, `title`, `desc`, `steps[]` (jeder Step: `id`, `transition`)
- **`VISITED_KEY` / `FAV_KEY`** – localStorage-Schlüssel; `safeLoad(key)` lädt mit try/catch
- **`nodes[]`** – 22 Denkschulen, jede mit `id`, `label`, `era`, `core`, `detail`, `reps[]`
- **`edges[]`** – ~40 Verbindungen mit `s` (source-id), `t` (target-id), `type`, `label`
- **`ERA_COLORS`** – Farbzuordnung pro Epoche
- **`EDGE_COLORS`** – Farbzuordnung pro Verbindungstyp (`influences`, `continues`, `criticizes`, `synthesizes`)
- **`NODE_YEARS`** – Zeitspannen pro Denkschule für die Zeitleisten-Ansicht
- **`PHILOSOPHER_DOTS[]`** – Einzelne Philosophen mit Geburtsjahr für Zeitleistenpunkte

### 2. Layout-Engine (`layoutNodes()`)

Ordnet Knoten in horizontalen Swim-Lanes nach Epoche an. Gibt `posMap` (id → {x, y, w, h}), `totalWidth` und `totalHeight` zurück. Kanten, deren `s` oder `t` nicht in `posMap` liegt, werden gefiltert.

### 3. Render & Interaktion (D3.js)

- SVG wird direkt auf `#main-svg` gezeichnet, alle Inhalte in `<g class="zoom-layer">` (D3 zoom)
- Kanten als `<path class="edge-path">` mit SVG-Arrowhead-Markern (`<defs>`)
- Knoten als `<g class="node-group">` mit `<rect>`, Era-Tag und Label
- **Hover**: hebt verbundene Kanten hervor (`.highlighted` / `.dimmed`); respektiert aktiven Lernpfad
- **`showNodePanel(d, transitionText = null)`** – zentrale Panel-Funktion: markiert visited, befüllt `#panel-content` (inkl. fav-btn, connections, quiz-section), öffnet Panel
- **`openPanelForNode(d)`** – Alias für `showNodePanel(d)`, genutzt vom Zeitleisten-Code

### 4. Features (alle in `start.html`)

| Feature | HTML-Elemente | Key-JS |
|---|---|---|
| **Suche** | `#search-input` | live filter auf label/core/reps, `.search-match` / `.search-dim` |
| **Lernfortschritt** | `#progress-wrap`, `#progress-bar-fill` | `visited` Set, `favorites` Set, `updateProgress()`, localStorage |
| **Lernpfade** | `#path-bar`, `#path-stepper` | `PATHS[]`, `startPath()`, `exitPath()`, `goToStep()`, `applyPathHighlight()` |
| **Quiz** | dynamisch in `#quiz-section` | `generateQuestions(node)`, `renderQuiz(node)`, `shuffle()` (Fisher-Yates), `pick()` |
| **Zeitleiste** | `#view-toggle`, `#timeline-wrap`, `#timeline-svg` | `NODE_YEARS`, `PHILOSOPHER_DOTS[]`, `renderTimeline()` (lazy; `timelineRendered`-Flag wird bei Resize zurückgesetzt) |
| **Mobile** | `#legend-toggle`, `@media ≤640px` | Bottom-Sheet-Panel, collapsible legend, D3 zoom auf SVG |

## Quiz-Implementierungsdetails

- **Satzerkennung**: Regex `^.+?[.!?](?=\s+[A-ZÄÖÜ„]|\s*$)` – erkennt Satzende nur vor Großbuchstaben, vermeidet Abkürzungen (`v. a.`, `z. B.`)
- **Distraktor-Deduplication**: Vertreter-Frage schließt Personen aus, die auch im `reps[]`-Array des abgefragten Knotens stehen
- **Verbindungsfragen**: Nur ausgehende Kanten (`e.s === node.id`); Frageformulierung per `questionMap` (key = `e.type`)
- **Zugänglichkeit**: `aria-label` aller Antwort-Buttons wird nach Auflösung gesetzt (`– richtige Antwort` / `– falsche Antwort`)

## Neue Denkschulen hinzufügen

1. Objekt zu `nodes[]` hinzufügen (Felder: `id`, `label`, `era`, `core`, `detail`, `reps[]`)
2. `era` muss einem der fünf Einträge in `ERAS[]` entsprechen
3. Kanten in `edges[]` eintragen (`s`/`t` = ids, `type` = einer der vier Typen)
4. Zeitspanne in `NODE_YEARS` und Philosophen-Dots in `PHILOSOPHER_DOTS[]` ergänzen

## Deployment (GitHub Pages)

- **Workflow**: `.github/workflows/deploy.yml` – deployt bei jedem Push auf `main` (auch manuell via `workflow_dispatch`)
- **Artifact**: Nur `index.html` und `start.html` werden in `_site/` kopiert und als Pages-Artifact hochgeladen — kein Repo-Overhead (CLAUDE.md, .github/ etc.)
- **`cancel-in-progress: true`**: Veraltete Deployments werden abgebrochen, sobald ein neuerer Push kommt
- **`index.html`**: Redirect-Seite von `/philosophen/` → `start.html` via `location.replace()` (JS) mit `<noscript>`-Fallback; kein `<meta http-equiv="refresh">`
- **Pages-Quelle** muss in Repo-Settings → Pages auf **„GitHub Actions"** gesetzt sein

## Branch-Konvention

Feature-Branches nach Schema `claude/<feature-name>` – je Issue ein Branch und PR.
