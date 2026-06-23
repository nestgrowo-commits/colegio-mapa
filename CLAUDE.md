# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static, self-contained school building navigation system with two interactive floor maps. No build step, no dependencies, no package manager — just open the HTML files in a browser.

**Files:**
- `index.html` — First floor interactive map with A* pathfinding and route visualization
- `tercer-piso.html` — Third floor virtual photo tour with directional navigation
- `fotos/` — JPG photos for first floor locations (naming: `S101.JPG`, `bañoW.JPG`, etc.)
- `fotos-3/` — JPG photos for third floor tour (referenced but may not be present locally)

## Architecture

### index.html (First Floor)

Three core data structures drive the entire page:

**`LOCS`** — 25 location definitions, each with `{ name, type, svgId, photo, terms }`. The `svgId` links to an SVG element; `terms` are the normalized search aliases.

**`G`** (navigation graph) — ~35 nodes with `{ x, y, nb }` where `nb` maps neighbor node IDs to edge costs. Used exclusively by the A* pathfinder.

**`LOC_NODE`** — Maps location IDs (keys of `LOCS`) to graph node IDs (keys of `G`). This bridges the two structures above.

Key functions: `findPath()` (A*), `navigateTo()`, `drawRoute()` (SVG polyline + animated markers), `makeSteps()`, `doSearch()` / `showSugs()` (fuzzy search with accent normalization via `norm()`).

### tercer-piso.html (Third Floor)

**`G`** (photo graph) — ~35 photo nodes with `{ img, name, fwd, bck, lft, rgt }` for directional links. Navigation traverses this graph one node at a time with CSS fade transitions.

**`ROOMS`** — 8 searchable destinations each with a preset `route` array of node IDs for guided tours.

Key functions: `goTo()` (navigate + fade), `go()` (directional input), `startRoute()` / `updateProgress()` / `buildSidebar()` (guided tour), `startAuto()` (2.4s autoplay).

## Key Conventions

- **All UI text is Spanish.** Location names, instructions, and button labels use Spanish; accent normalization (`NFD` + strip combining chars) is required for search.
- **No external CSS files.** All styles are in `<style>` blocks within each HTML file.
- **SVG highlighting** uses `.highlight` class → `fill: #FFC107 !important`.
- **Photo errors** are handled silently: `img.onerror` hides the photo container rather than showing broken images.
- **Location types** used in `LOCS`: `classroom`, `bathroom`, `stairs`, `elevator`, `special`, `service`, `sport`, `play`, `parking`, `entrance`.
- When adding a new navigable location to `index.html`, three places must be updated: `LOCS`, `G` (add node(s) with neighbor costs), and `LOC_NODE`.
