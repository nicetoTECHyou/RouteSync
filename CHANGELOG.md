# RouteSync — Changelog

Alle Änderungen sind chronologisch dokumentiert. Versionsnummern folgen [Semantic Versioning](https://semver.org/).

| Typ | Format | Beschreibung |
|-----|--------|-------------|
| **Patch** | x.x.Z | Bug Fixes, kleine Korrekturen |
| **Minor** | x.Y.0 | Große Fixes, Security, Route-Änderungen |
| **Major** | X.0.0 | Neue Features, Breaking Changes |

---

## [0.3.6] — 2026-04-13

### Patch — ElevationProfile Marker Crash Fix (Production)

#### Fixes
- **maplibre-gl ESM/CJS Interop**: `maplibre-gl` v5.22 ist ein ESM-Paket mit CJS-`main`-Entry. Im Webpack-Production-Bundle liefert `import maplibregl from 'maplibre-gl'` ein Wrapper-Objekt `{ default: {...} }` statt des Namespace. Dadurch war `maplibregl.Marker` in der Produktion `undefined`, was bei jedem Hover über das Höhenprofil zum App-Crash führte (hunderte Fehler pro Sekunde).
- **Lösung (3 Ebenen)**:
  1. `transpilePackages: ['maplibre-gl']` in `next.config.ts` — Next.js transpiliert maplibre-gl durch SWC, was den CJS→ESM Default-Export korrekt handhabt (Root-Cause-Fix)
  2. Defensiver Import-Pattern in `ElevationProfile.tsx`: `(maplibreglRaw as any).default ?? maplibreglRaw` + explizite `MarkerCtor`-Prüfung mit `try-catch` (Defense-in-Depth)
  3. Defensiver Import-Pattern in `RoutePanel.tsx`: Named Import `LngLatBounds` über Default-Import mit `.default`-Fallback (verhindert potenziellen Route-Bounds-Crash)

#### Geänderte Dateien
- `next.config.ts` — `transpilePackages: ['maplibre-gl']`
- `src/components/map/ElevationProfile.tsx` — defensiver CJS/ESM Interop-Import, Marker availability check
- `src/components/sidebar/RoutePanel.tsx` — defensiver CJS/ESM Interop-Import für `LngLatBounds`

---

## [0.3.5] — 2026-04-13

### Patch — ElevationProfile Marker Crash Fix

#### Fixes
- **Marker-Crash im Höhenprofil**: `ElevationProfile.tsx` nutzte `(window as any).maplibregl.Marker()` für den Hover-Marker auf der Karte. Bei fehlendem oder asynchron geladenem `maplibre-gl` global führte dies zu einem Runtime-Crash der gesamten App.
- **Lösung**: Statischer `import type { Map as MapLibreMap }` (nur Typ-Import, kein runtime-Bundle). Marker-Erstellung über `await import('maplibre-gl')` (dynamischer Import) mit `try-catch` Wrapper. Funktion `handleMouseMove` als `async` markiert. Bei Import-Fehler wird nur ein `console.warn` ausgegeben — die App läuft weiter.

#### Geänderte Dateien
- `src/components/map/ElevationProfile.tsx` — type-only Import, async dynamic import, try-catch

---

## [0.3.0] — 2026-04-13

### Major Feature — Höhenprofil

#### New Feature
- **Höhenprofil / Elevation Profile**: Interaktives SVG-Diagramm als Overlay oben an der Karte
  - X-Achse = Streckenkilometer, Y-Achse = Höhe in Metern
  - Farbcodierte Steigung: Grün (flach ≤2%), Gelb (leicht ≤5%), Orange (mittel ≤8%), Rot (steil >8%)
  - Hover-Tooltip zeigt: km-Position, Höhe, Steigung in %
  - Hover platziert Marker auf der Karte an der entsprechenden Position
  - Aufklappbar: Kompakt-Modus zeigt nur Distanz/Auf-/Abstieg, aufgeklappt zeigt vollständiges Diagramm
  - Höhendaten von Open-Meteo Elevation API (kostenlos, kein Key)
  - Auto-Sampling bei langen Routen (max 2000 Punkte), Batching (500 pro Request)
  - Cache vermeidet redundante API-Aufrufe

#### Geänderte Dateien
- `src/components/map/ElevationProfile.tsx` — NEU: Höhenprofil-Komponente
- `src/lib/elevation.ts` — NEU: Elevation API Client + Profil-Berechnung
- `src/components/map/MapView.tsx` — ElevationProfile eingebunden

---

## [0.2.8] — 2026-04-13

### Features & Fixes — 4 Changes

#### Fixes
- **POI Popup z-index**: Popups liegen jetzt ÜBER den POI-Markern (vorher verdeckt)
- **Navigationsexport komplett überarbeitet**:
  - Google Maps: Fahrrad-Modus (`travelmode=bicycling`), alle Via-Punkte als `waypoints`-Parameter, kein eigenes Routing mehr
  - Apple Maps: Start (`saddr`) + Ziel (`daddr`) + Via-Punkte als mehrfache `daddr`-Parameter
  - Organic Maps: Alle Waypoints (nicht nur Start/Ziel) als Route übergeben

#### New Features
- **Sonnenaufgangs-Kompass**: Toggle in Ebenen-Menü — zeigt SVG-Kompass mit Sonnenaufgangs-/untergangsrichtung, Azimut in Grad + Himmelsrichtung, Uhrzeiten. Berechnet nach NOAA-Formel basierend auf Kartenmitte und aktuellem Datum.
- **Notizen auf der Karte**: Rechtsklick → "Notiz hinzufügen" — erstellt bearbeitbare/löschbare Notiz-Marker. Popup mit Bearbeiten/Löschen-Buttons. 6 Farboptionen. Eigener Store (`useNoteStore`).

#### Geänderte Dateien
- `src/components/map/MapView.tsx` — POI popup z-index, Note-Marker, Sunrise-Overlay, Kontextmenü-Erweiterung
- `src/components/map/MapControls.tsx` — Sunrise-Compass Toggle im Layer-Menü
- `src/app/globals.css` — `.poi-popup` z-index CSS
- `src/lib/export.ts` — Google Maps/Apple Maps/Organic Maps komplett neu
- `src/lib/sun.ts` — NEU: Sonnenstands-Berechnung
- `src/store/useMapStore.ts` — `showSunriseCompass` State
- `src/store/useNoteStore.ts` — NEU: Notiz-Store
- `src/types/index.ts` — `MapNote` Interface

---

## [0.2.7] — 2026-04-13

### Feature — Ladesäulen-Details erweitert

#### Neue Infos bei Ladesäulen (POI Popup)
- **Anschlüsse/Steckertypen**: Typ 2, Typ 2 CCS, CHAdeMO, Schuko, CEE Blau/Rot, Typ 1, Tesla Supercharger/CCS — jeweils mit Ladeleistung (z.B. 22 kW), Anzahl und Kabel-Info
- **Gesamtleistung**: `charging_station:output` Tag
- **Bezahlung**: Kontaktlos, App, Karte, Bargeld, Authentifizierungsmethode, Kostenlos/Kostenpflichtig
- **Website**: Als klickbarer Link mit bereinigter URL-Anzeige
- **Sidebar-Kurzinfo**: Neue `ChargingSummary`-Komponente zeigt wichtigste Anschlüsse + Leistung + Kosten direkt in der POI-Liste

#### Geänderte Dateien
- `src/components/map/MapView.tsx` — `formatPOIDetails()` Ladesäulen-Block komplett erweitert
- `src/components/sidebar/POIPanel.tsx` — `ChargingSummary` Komponente für Sidebar

---

## [0.2.6] — 2026-04-13

### Major Update — 10 Changes

#### New Features
- **POI Radius bis 100km**: Erweitert von 10km auf 100km (5km, 10km, 25km, 50km, 100km)
- **Emoji POI-Icons**: Alle 16 POI-Kategorien haben jetzt professionelle Emoji-Icons statt Buchstaben-Circles
- **3D Terrain**: Echtes 3D-Gelände mit MapLibre GL DEM-Tiles. Karte kann geneigt/gedreht werden (Drag-Rotate). Terrain exaggeration 1.5x
- **Multi-Navigationsexport**: Export für GPX, KML, GeoJSON, Google Maps, Apple Maps, Waze, TomTom, Organic Maps
- **Auto-Cities als Via**: Button "Add Cities as Via" — sampelt Route alle ~30km, Reverse-Geocodiert via Nominatim, fügt Städte/Gemeinden automatisch als Via-Punkte ein
- **Barrieren-Vermeidung**: Checkbox "Avoid barriers/steps/bollards" nutzt Safety-Profile für barrierefreie Routen
- **Kick User**: Host kann Teilnehmer aus der Session werfen (roter Kick-Button)

#### UI/UX Verbesserungen
- **Map Controls kompakt**: Alle Controls in einer kleinen Panel unten-links. Keine Überlappung mehr mit Header/Session-Info
- **Icon-only Style-Buttons**: Kartenstile als kompakte Icon-Row statt großer Buttons mit Text
- **3D/Pitch Controls**: Bei aktivem 3D-Modus: Neigung oben/unten/Reset Buttons

#### Entfernt
- **GPS Location Button**: Komplett entfernt. GPS wird nicht benötigt — POI-Suche startet von Kartenmitte
- **Locate-Button** aus RoutePanel entfernt

#### Session-Sync Info
- Hinweis in SessionPanel: "Live session sync wird in v0.3.0 verfügbar sein. Aktuell sind Sessions lokal im Browser."

#### Geänderte Dateien
- `src/types/index.ts` — emoji-Feld statt icon, terrain3d in MapSettings
- `src/store/useMapStore.ts` — terrain3d, avoidBarriers States
- `src/lib/export.ts` — KML, GeoJSON, Apple Maps, Waze, TomTom, Organic Maps Export
- `src/lib/geocode.ts` — reverseGeocode() Funktion
- `src/lib/routing.ts` — avoidBarriers Support
- `src/components/map/MapView.tsx` — 3D Terrain, Emoji POI-Marker
- `src/components/map/MapControls.tsx` — Komplett neu: kompakt, bottom-left, 3D-Controls
- `src/components/sidebar/POIPanel.tsx` — 100km Radius, Emoji-Icons
- `src/components/sidebar/RoutePanel.tsx` — Kein GPS, Barrieren, Auto-Cities
- `src/components/sidebar/SessionPanel.tsx` — Kick-Button, Sync-Info
- `src/components/sidebar/ExportPanel.tsx` — Multi-Navigationsexport

---

## [0.1.2] — 2026-04-13

### Hotfix — React Hydration Error #418

#### Fixes
- **Hydration Mismatch**: `JoinOverlay.tsx` nutzte `useTheme()` von next-themes direkt im Render. `theme` ist auf dem Server `undefined`, im Client `"dark"` → unterschiedlicher Output → Hydration Error #418.
- **Lösung**: `mounted`-State mit `useEffect(() => setMounted(true), [])`. Theme-Toggle wird erst nach Client-Mount gerendert.
- **`resolvedTheme`** statt `theme` verwendet (korrekt bei `enableSystem: true`).

#### Dateien geändert
- `src/components/session/JoinOverlay.tsx`

---

## [0.1.1] — 2026-04-13

### Hotfix — GitHub Pages Asset 404

#### Fixes
- **basePath + assetPrefix**: `next.config.ts` jetzt mit `basePath: "/RouteSync"` und `assetPrefix: "/RouteSync"` für korrekte Asset-Pfade auf GitHub Pages Subdirectory-Deploy (`https://<user>.github.io/RouteSync/`)
- **Placeholder API Route entfernt**: `src/app/api/route.ts` gelöscht (inkompatibel mit Static Export)
- **Static Export aktiviert**: `output: "export"` + `distDir: "dist"` statt `output: "standalone"`

#### Geändert
- Alle Asset-Referenzen in `index.html` nutzen jetzt `/RouteSync/_next/...` statt `/_next/...`
- `dist/` enthält keine Dev-Artefakte mehr (dev/, build-manifest, etc.)

---

## [0.1.0] — 2026-04-13

### Erstveröffentlichung — RouteSync v0.1.0

Kompletter Neubau basierend auf der Karten- und Routenlogik von TwitchCoPilot v4.3.3. Radikale Bereinigung aller Twitch-, Streaming-, Navi- und OBS-spezifischen Komponenten.

#### Neue Features
- **Karten-Modi**: 5 Kartenstile (Street, Dark, Satellite, Topo, Terrain3D) via MapLibre GL
- **Routenberechnung**: 7 BRouter-Profile (FastBike, Trekking, MTB, Safety, Car-Fast, Car-Eco, Walk)
- **Routen-Alternativen**: 3 automatisch berechnete Alternativen (Kürzeste, Schnellste, Sicherste) mit Post-Processing-Validierung
- **POI-System**: 16 POI-Kategorien mit Overpass API, klickbare Filter-Chips, "Add to Route" Buttons in Popups
- **Kollaborative Sitzung**: 6-stelliger Session-Code, Host/Editor/Viewer-Rollen, Schreibrechte-Verwaltung
- **Web-Chat**: Echtzeit-Chat für alle Sitzungsteilnehmer mit Teilnehmerfarben und Auto-Scroll
- **Export**: GPX, Google Maps Link (Clipboard), JSON (1:1 Import/Export)
- **Mobile Listener Mode**: Vollbild-Karte + Chat auf kleinen Screens (< 768px)
- **Professionelles Desktop-UI**: 5-Tab Sidebar, Glass-Morphism, Dark/Light Theme (Teal-Palette)
- **Layer-Kontrolle**: Hillshade-Overlay, Straßennamen, POI-Marker, Routenlinie ein/aus
- **Kontextmenü**: Rechtsklick auf der Karte für Start/Via/Ziel-Platzierung

#### Technisch
- Next.js 16 (App Router), TypeScript, Tailwind CSS v4
- MapLibre GL 5.22, Zustand 5, shadcn/ui, Lucide Icons
- 0 API-Keys benötigt (CARTO, BRouter, Nominatim, Overpass — alle kostenlos)
- Lint: 0 Errors, 0 Warnings

#### Entfernt (vs. TwitchCoPilot v4.3.3)
- Twitch Bot (tmi.js), Chat-Befehle, Alerts, Moderation
- OBS-Overlay, StreamerTab, VotingPanel
- SkyChart/Sternenkarte, Stars-Modus
- GPS-Tracking, Navi-Modus, Demo-Modus, TTS, Auto-Reroute
- DriveInfoPanel, NavArrow, MuteButton, WeatherWidget, POIShortcuts
- PeerJS-Sync, MQTT-Sync

#### Dateien (24 neu erstellt)
- `src/types/index.ts` — TypeScript Interfaces
- `src/lib/utils.ts`, `session.ts`, `geocode.ts`, `routing.ts`, `overpass.ts`, `export.ts`
- `src/store/useRouteStore.ts`, `useSessionStore.ts`, `usePOIStore.ts`, `useMapStore.ts`
- `src/components/map/MapView.tsx`, `MapControls.tsx`
- `src/components/sidebar/Sidebar.tsx`, `RoutePanel.tsx`, `POIPanel.tsx`, `SessionPanel.tsx`, `ChatPanel.tsx`, `ExportPanel.tsx`
- `src/components/session/JoinOverlay.tsx`, `SessionHeader.tsx`
- `src/app/globals.css`, `layout.tsx`, `page.tsx`
