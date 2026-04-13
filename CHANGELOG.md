# RouteSync — Changelog

Alle Änderungen sind chronologisch dokumentiert. Versionsnummern folgen [Semantic Versioning](https://semver.org/).

| Typ | Format | Beschreibung |
|-----|--------|-------------|
| **Patch** | x.x.Z | Bug Fixes, kleine Korrekturen |
| **Minor** | x.Y.0 | Große Fixes, Security, Route-Änderungen |
| **Major** | X.0.0 | Neue Features, Breaking Changes |

---

## [0.4.2] — 2026-04-14

### Minor — Code-Review: Routing, Elevation, Sync, Export Fixes

#### Critical Fixes
- **Walk-Profile: 3x identische API-Calls**: Alle 3 Routen-Kategorien (`shortest`, `fastest`, `safest`) nutzten Profil `walk` → 3 identische BRouter-Requests. Der Duplicate-Skip schloss `walk` explizit aus. Walk-Route dauerte 3x so lange (9-15s statt 3-5s).
  - **Fix**: Walk-Kategorien auf 1 Eintrag reduziert, Profile-Dedup für alle Profile aktiviert
- **Elevation 0m = Meereshöhe als „ungültig" behandelt**: `elevation === 0` interpolierte legitime 0m-Werte (Küste, Niederlande) mit Nachbarwerten → sichtbare Plateau-Artefakte im Höhenprofil.
  - **Fix**: Nur `null`/`undefined`/`NaN` interpoliert, 0m beibehalten
- **Note-Marker DOM-Erstellung 100% dupliziert**: ~60 Zeilen Code exakt kopiert zwischen Initial-Render und Subscribe-Callback in MapView.
  - **Fix**: Extrahiert in `createNoteMarkerEl()` und `createNotePopupHtml()` Helper-Funktionen

#### High Fixes
- **Anti-Echo-Mechanismus komplett kaputt**: `setApplyingRemoteFn(true)` überschrieb die Setter-Funktion mit dem Boolean `true` statt sie aufzurufen. Jeder Remote-Update löste ein unnötiges Echo-Roundtrip aus.
  - **Fix**: `_applyingRemoteSetter?.(true)` — Setter wird jetzt aufgerufen statt überschrieben
- **Join-Timeout leckt in neue Sessions**: `createSession()` clearte den Join-Timeout nicht → stale Timeout setzte `connectionStatus: 'error'` auf der falschen Session.
  - **Fix**: Timeout-Clear am Anfang von `createSession()`
- **avoidBarriers fügt kontraproduktive URL-Parameter hinzu**: `avoidHighways=false` erlaubte explizit Highways — das Gegenteil von „Barrieren vermeiden".
  - **Fix**: Entfernt (BRouter nutzt Safety-Profile für Barriere-Vermeidung)
- **Apple Maps Multi-Stop URL falsch**: `&daddr=...&daddr=...` — Apple Maps nutzt nur den letzten `daddr`. Via-Punkte wurden ignoriert.
  - **Fix**: Via-Punkte mit `+` Separator an `daddr` angehängt
- **TomTom-Link generiert HERE Maps URL**: `exportTomTomLink()` linkte zu `share.here.com` statt TomTom.
  - **Fix**: Umbenannt zu `exportHereMapsLink()`, Label zu „HERE WeGo"
- **MapView: Store-Subscriptions ohne Selector**: `useRouteStore()` und `useSessionStore()` ohne Selector → jede Store-Änderung re-rendered die 1100-Zeilen MapView-Komponente.
  - **Fix**: Individuelle Selektoren für alle Store-Werte
- **Sonnenaufgangs-Kompass aktualisiert sich nicht bei Map-Pan**: Effect hing nur von `showSunriseCompass` und `styleLoadCount` ab, nicht von Map-Bewegung.
  - **Fix**: `moveend`-Listener rebuildet den Kompass bei jedem Pan/Zoom
- **Sonnenuhrzeit in UTC statt Lokalzeit**: `timeZone: 'UTC'` zeigte für Berlin (CEST) falsche Zeiten (z.B. 21:45 statt 23:45).
  - **Fix**: `timeZone: 'UTC'` entfernt
- **Session-Codes mit Math.random() statt crypto**: `Math.random()` ist nicht kryptographisch sicher, vorhersagbar bei Beobachtung mehrerer Codes.
  - **Fix**: `crypto.getRandomValues()` für Session-Codes und Participant-IDs

#### Medium Fixes
- **Duplicate participant-join nicht abgefangen**: PeerJS-Retries konnten denselben Teilnehmer mehrfach in die Liste eintragen.
  - **Fix**: Duplicate-Guard bei `participant-join` Nachricht
- **followMapView bei Session-Leave nicht zurückgesetzt**: Flag blieb `true` und galt für die nächste Session.
  - **Fix**: Reset auf `false` in `leaveSession()`
- **Dead Code entfernt**: `getApplyingRemoteFn()` nie aufgerufen, redundante Type-Casts auf `currentJoinTimeoutRef`
- **Production console.log in elevation.ts**: 3 Log-Aufrufe in Produktion → interne Timing-Daten im Browser-Console sichtbar.
  - **Fix**: Gated hinter `process.env.NODE_ENV !== 'production'`
- **Canvas contextmenu Listener nie aufgeräumt**: Memory Leak beim MapView Unmount.
  - **Fix**: Named Handler mit Cleanup im useEffect-Return

#### Geänderte Dateien
- `src/lib/routing.ts` — Walk-Profile Dedup, avoidBarriers-Parameter entfernt
- `src/lib/elevation.ts` — 0m-Elevation-Fix, Production-Logs entfernt
- `src/lib/export.ts` — Apple Maps URL-Fix, TomTom→HERE Umbenennung, VERSION→0.4.2
- `src/lib/geocode.ts` — User-Agent VERSION→0.4.2
- `src/lib/session.ts` — crypto.getRandomValues() statt Math.random()
- `src/lib/sun.ts` — Lokalzeit statt UTC für formatSunTime()
- `src/store/useSessionStore.ts` — Anti-Echo-Fix, Join-Timeout-Clear, Duplicate-Guard, followMapView-Reset, Dead-Code-Removal
- `src/components/map/MapView.tsx` — Note-Marker-Dedup, Store-Selektoren, Sunrise-Kompass moveend, Canvas-Listener-Cleanup
- `src/components/sidebar/ExportPanel.tsx` — HERE WeGo statt TomTom

---

## [0.4.1] — 2026-04-14

### Patch — MapView Temporal Dead Zone Crash Fix (Production)

#### Fixes
- **MapView TDZ-Crash**: `let mapViewCleanup` wurde am Ende des `useEffect`-Callbacks deklariert (Zeile 433), aber bereits vorher (Zeile 387) zugewiesen. In der JavaScript Temporal Dead Zone (TDZ) wirft jeder Zugriff auf eine `let`/`const`-Variable vor ihrer Deklaration einen `ReferenceError`. Im Development-Modus trat der Fehler nicht auf (andere Transpilierung durch SWC), aber im Production-Build (Terser-Minifier) wurde `mapViewCleanup` zu `c` minimiert → `Cannot access 'c' before initialization`. Die App lud zwar (PeerSync-Log sichtbar), aber der MapView useEffect stürzte synchron ab → Weiße Seite.
  - **Fix**: Deklaration `let mapViewCleanup` vor die erste Zuweisung verschoben.

#### Geänderte Dateien
- `src/components/map/MapView.tsx` — TDZ-Fix: `let mapViewCleanup` vor Zuweisung deklariert

---

## [0.4.0] — 2026-04-14

### Minor — Security & Sync Bugfixes (Code-Review)

#### Security Fixes
- **Window-Globals eliminiert**: `window.__rsBroadcast`, `window.__rsApplyingRemote`, `window.__rsMapRef`, `window.__editNote`, `window.__deleteNote`, `window.__addPOIAsVia`, `window.__addPOIAsDest` — alle durch module-scoped Functions ersetzt. Verhindert, dass Browser-Extensions oder injizierte Scripts PeerSync-Nachrichten fälschen oder State manipulieren können.
  - `setBroadcastFn()` / `getBroadcastFn()` — module-scoped Broadcast-Zugriff
  - `setMapRefGetter()` — module-scoped Map-Ref-Zugriff (ohne circular imports)
  - Event Delegation für POI/Note-Buttons (`data-action`, `data-note-action`) statt `onclick="window.__..."` — verhindert XSS bei manipulierten IDs
- **XML-Injection in GPX/KML-Export**: Waypoint-Namen wurden ungefiltert in XML interpoliert. Ein Name wie `<script>alert(1)</script>` erzeugte kaputte XML. Neue `escXml()`-Funktion escaped `& < > " '` vor dem Einfügen.
- **Prisma Query-Logging im Production-Build**: `log: ['query']` loggte alle SQL-Queries. Jetzt nur in Development aktiv (`process.env.NODE_ENV !== 'production'`).

#### Bug Fixes (Sync)
- **PeerSync Broadcast wurde SILENT GESKIPPT**: `setupRouteStoreSync()` aktualisierte `lastSyncedWaypointsJson` SOFORT vor dem Debounce-Callback. Im Callback wurde gegen den bereits aktualisierten Wert geprüft → Bedingung immer `false`. Waypoint-Änderungen wurden nie an Peers gesendet.
  - **Fix**: `lastSynced` wird nur NACH erfolgreichem Broadcast im Debounce-Callback aktualisiert
- **Disconnect-Status immer "connected"**: `onConnectionClose` setzte fälschlich `'connected'` wenn `getConnectionCount() === 0` (Tautologie: `hasConnections() === false` garantiert `count === 0`).
  - **Fix**: Setzt korrekt `'disconnected'`
- **Double-Broadcast**: `updateSessionWaypoints/Routes/AltRoutes` broadcasteten explizit UND der Auto-Sync-Subscription broadcastete auch → jede Nachricht 2× bei Peers.
  - **Fix**: Explizite Broadcasts entfernt — Auto-Sync reicht aus
- **Join-Timeout nie aufgeräumt**: Der 10s-Timeout in `joinSession` wurde bei erfolgreichem Join nicht gecleart. `leaveSession` innerhalb von 10s → stale Error.
  - **Fix**: Timeout-Ref gespeichert, in `leaveSession` gecleart
- **Dead Code**: `isApplyingRemoteState`-Variable deklariert aber nie gelesen. `getSessionPeerId` importiert aber nie verwendet.
  - **Fix**: Entfernt

#### Memory Leak Fixes
- **MapView moveend Listener**: `mapViewDebounce`-Timeout wurde nie aufgeräumt beim Unmount. `map.on('moveend', ...)` wurde nie mit `map.off()` entfernt.
  - **Fix**: Named handler + Cleanup-Funktion im useEffect-Return
- **Note/POI Event Handlers**: Window-Globals für Note/POI-Buttons wurden nie aufgeräumt.
  - **Fix**: Event Delegation auf Map-Container mit `addEventListener`/`removeEventListener` im Effect-Cleanup

#### Other Fixes
- **clearWaypoints reset nicht alle Indices**: `selectedRouteIdx` und `highlightedRouteIdx` blieben nach `clearWaypoints()` auf alten Werten → potenziell stale Index-Referenzen.
  - **Fix**: Reset auf `0` / `-1` in `clearWaypoints()`

#### Geänderte Dateien
- `src/store/useSessionStore.ts` — Module-scoped Bridge, Sync-Bugfixes, Double-Broadcast-Fix, Timeout-Cleanup
- `src/components/map/MapView.tsx` — Event Delegation, moveend Cleanup, Window-Globals eliminiert
- `src/lib/export.ts` — `escXml()` für XML-Injection-Schutz, VERSION → 0.4.0
- `src/lib/db.ts` — Prisma Query-Logging nur in Development
- `src/store/useRouteStore.ts` — `clearWaypoints` reset alle Indices

---

## [0.3.7] — 2026-04-13

### Patch — API Fehlerbehandlung & Rate-Limit Fix

#### Fixes
- **Elevation Endlos-Retry-Loop**: Wenn die Open-Meteo Elevation API mit 429 (Too Many Requests) antwortete, setzte `setError(true)` den `lastFetchKey` NICHT. Der useEffect-Guard `lastFetchKey === fetchKey && profile.length > 0` schlug fehl → die Fetch-Funktion wurde unendlich wiederholt (hunderte API-Calls pro Sekunde).
  - **Lösung**: `attemptedKeyRef` (useRef) trackt, welche fetchKey bereits versucht wurde (Erfolg ODER Fehler). Ein Reset passiert nur bei neuer Route.
- **Elevation 429 Rate-Limit Storm**: 5 Batches à 100 Punkte wurden ohne Delay hintereinander abgeschossen. Bei 429 gab es keinen Retry und kein Backoff.
  - **Lösung**: 1.1s Delay zwischen Batches, exponentieller Backoff bei 429 (2s, 4s), max 2 Retries, AbortController für sauberes Abbrechen bei Unmount/Route-Change.
- **Nominatim fehlender User-Agent**: Nominatim Usage Policy verlangt einen `User-Agent` Header. Ohne werden Requests langsamer oder blockiert.
  - **Lösung**: `User-Agent: RouteSync/0.3.6 (collaborative route planning)` bei allen Geocode- und Reverse-Geocode-Requests.
- **Hillshade + Terrain gleiche DEM-Source**: MapLibre warnt: "You are using the same source for a hillshade layer and for 3D terrain. Please consider using two separate sources to improve rendering quality."
  - **Lösung**: Zwei separate DEM-Sources (`hillshade-dem` und `terrain-dem`) mit denselben Tiles aber unabhängig voneinander. Style-Switch entfernt beide korrekt.
- **Export hartcodierte Version**: GPX/KML/JSON-Export referenzierte `v0.2.0` statt der aktuellen Version.
  - **Lösung**: Zentrale `VERSION`-Konstante, dynamisch in alle Export-Formate eingebaut.

#### Geänderte Dateien
- `src/lib/elevation.ts` — Komplett-Rewrite: Batch-Delay, Retry mit Backoff, AbortController
- `src/components/map/ElevationProfile.tsx` — `attemptedKeyRef` statt `lastFetchKey`-Guard, AbortController
- `src/lib/geocode.ts` — User-Agent Header für Nominatim
- `src/components/map/MapView.tsx` — Separate DEM-Sources für Hillshade und Terrain
- `src/lib/export.ts` — Dynamische VERSION-Konstante

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
