# RouteSync — Changelog

Alle Änderungen sind chronologisch dokumentiert. Versionsnummern folgen [Semantic Versioning](https://semver.org/).

| Typ | Format | Beschreibung |
|-----|--------|-------------|
| **Patch** | x.x.Z | Bug Fixes, kleine Korrekturen |
| **Minor** | x.Y.0 | Große Fixes, Security, Route-Änderungen |
| **Major** | X.0.0 | Neue Features, Breaking Changes |

---

## [1.1.0] — 2026-04-15

### Minor — "Typ 2 AC Charge": Dedizierte Overpass-Query, 1m Breite Docs, Professional README

Die Ladesäulen-Suche (Quick-Action Button im Header) verwendet jetzt eine dedizierte Overpass-Query die spezifisch nach `socket:type2=yes` sucht. Zuvor wurden alle charging_station geladen und clientseitig gefiltert — ineffizient und unzuverlässig. Die neue Query priorisiert AC-Ladepunkte (Typ 2) und schliesst reine DC-Schnelllader (CCS/CHAdeMO-only) auf Overpass-Ebene aus. Zusätzlich: README komplett auf Cargo Bike Navi-Fokus umgeschrieben mit 1m Breite Verification Dokumentation.

#### New Features
- **Dedizierte Typ 2 AC Overpass-Query**: `buildType2ChargingCenterQuery()` in `overpass.ts` — neue Funktion die gezielt nach `socket:type2=yes` sucht. Beinhaltet: Type 2 Socket-Filter, amenity=charging_station Fallback, Brand/Operator Regex (SE Europe), supercharge=yes. Max 15km dynamischer Radius.
- **type2Only Flag im Aggregator**: `AggregatorOptions.type2Only` — wenn `true`, wird die dedizierte Typ 2 Query statt der generischen Enhanced Charging Query verwendet. Der Header Quick-Action Button nutzt dieses Flag.
- **Professional README Rewrite**: Komplett neue README fokussiert auf Cargo Bike World Travel: 1m Width Verification Technologie-Tabelle, Typ 2 AC Charging Erklärung, Southeast Europe Abdeckung, SVG-Icon-System Dokumentation, 8-Länder EV-Datensatz-Referenz.

#### Architecture
- **`overpass.ts`**: Neue exportierte Funktion `buildType2ChargingCenterQuery(lat, lon, radius)` — dedizierte Typ 2 / AC Overpass-Query mit 15km Hard-Cap.
- **`poi-aggregator.ts`**: `AggregatorOptions` erweitert um `type2Only?: boolean`. Center-Such-Flow prüft Flag und wählt entsprechende Query.
- **`HeaderBar.tsx`**: Quick-Action Button nutzt jetzt `type2Only: true` beim Aufruf von `searchPOIsAggregated()`.

#### Version Bump — 1.1.0
Konsistenter Version-Bump über alle 6 Dateien:

| Datei | Alt | Neu |
|-------|-----|-----|
| `VERSION` | 1.0.0 | 1.1.0 |
| `package.json` | 1.0.0 | 1.1.0 |
| `src/components/dashboard/HeaderBar.tsx` | 1.0.0 | 1.1.0 |
| `src/components/sidebar/Sidebar.tsx` | 1.0.0 | 1.1.0 |
| `src/lib/export.ts` | 1.0.0 | 1.1.0 |
| `src/lib/geocode.ts` | 1.0.0 | 1.1.0 |

#### Geänderte Dateien
- `src/lib/overpass.ts` — buildType2ChargingCenterQuery() hinzugefügt
- `src/lib/poi-aggregator.ts` — type2Only Flag, Typ 2 Query Integration
- `src/components/dashboard/HeaderBar.tsx` — type2Only=true, v1.1.0
- `VERSION` — 1.1.0
- `package.json` — 1.1.0
- `src/components/sidebar/Sidebar.tsx` — 1.1.0
- `src/lib/export.ts` — 1.1.0
- `src/lib/geocode.ts` — 1.1.0
- `README.md` — Komplett-Rewrite: Cargo Bike Navi, 1m Breite, Typ 2 AC
- `CHANGELOG.md` — v1.1.0 Eintrag

---

## [1.0.0] — 2026-04-15

### Major — "The Lastenrad Cockpit": Power-Leiste, Anti-Redundanz, Tooltips, Expert Features

Professionelles Lastenrad-Cockpit: Alle Redundanzen eliminiert, Header zur Lastenrad-Power-Leiste umgebaut, Tooltips für jedes interaktive Element, Expert-Features implementiert. Das Dashboard zeigt jetzt nur was der Lastenrad-Fahrer braucht — keine doppelten Steuerelemente, keine überflüssigen Kategorien im Header. Die obere Leiste ist ausschließlich für Fahrrad-Logistik-Filter reserviert.

#### Anti-Redundancy — Radikale Entschlackung
- **POI-Category-Chips aus Header ENTFERNT**: Restaurant, Café, Krankenhaus etc. waren doppelt — im Header UND in der rechten Sidebar (POIPanel). POI-Kategorien sind jetzt NUR in der rechten Sidebar. Header ist frei für Lastenrad-Filter.
- **Kartenstil-Switcher aus Header ENTFERNT**: 5 Emoji-Buttons (🗺️🌙🛰️⛰️🌄) waren doppelt — im Header UND auf der Karte (MapControls). Kartenstil-Umschaltung ist jetzt NUR als kompaktes Icon-Menü direkt auf der Karte (MapControls unten links).
- **avoidBarriers-Checkbox aus RoutePanel ENTFERNT**: War redundant mit dem neuen Header-Filter. Barriere-Check ist jetzt als Filter-Toggle in der Power-Leiste.
- **Logische Trennung**: Header = Lastenrad-Logistik, Rechte Sidebar = POI-Kategorien, Karte = Kartenstil/Layer.

#### New Feature: Lastenrad Power-Leiste (Header)
- **4 Toggle-Buttons** mit detaillierten Tooltips:
  - ⛔ **Barrieren**: Filtert Wege < 100m Breite oder physische Hindernisse (Umlaufperren, Poller). Aktiviert automatisch Sicherheitsprofil.
  - 🛣️ **Autobahnen**: Vermeidet motorisierte Schnellstraßen für sichere Fahrradrouten.
  - 🪨 **Schotter**: Priorisiert Asphalt. Schotter, Sand, unbefestigt werden gemieden (kritisch für 200kg Lastenrad).
  - ⚡ **Ladesäulen**: Zeigt nur ebenerdige/breite Säulen an (mind. 1m Zugang).
- **Visuelles Feedback**: Aktive Filter leuchten mit Farbhintergrund + pulsierendem Punkt + Schatten-Glow.
- **Filter-Counter**: Badge zeigt "N Filter" wenn Filter aktiv.
- **Detaillierte Tooltips**: Jeder Filter hat zwei Tooltip-Varianten — Inaktiv (Erklärung) und Aktiv (Status + Auswirkung).

#### New Feature: Quick-Action-Panel (Header)
- **⚡ "Laden!" Notfall-Button**: Roter Emergency-Button in der Power-Leiste. Sucht sofort Ladesäulen im 15km-Umkreis der Kartenmitte, sortiert nach Distanz, flyt zur nächsten Station. Kein Seitentwechsel, kein Menü — ein Klick reicht.

#### New Feature: Tooltips für jedes interaktive Element
- **MapControls**: Alle Buttons (Zoom, Kompass, 3D, Layer, Kartenstil) haben jetzt Tooltips mit präziser Beschreibung. Map-Stile zeigen zusätzlich Untertitel (z.B. "Topografische Karte mit Höhenlinien").
- **RoutePanel**: Profil-Selector und Berechnen-Button haben Tooltips. Profil gesperrt wenn Barriere-Filter aktiv.
- **Header**: Session-Code, Teilnehmer, Verbindungsstatus, Settings — alles mit Tooltips.
- **Layer-Toggles**: Jeder Layer-Toggle zeigt "Titel (aktiv)" oder "Titel + Beschreibung" im Tooltip.

#### New Feature: Elevation-Highlight (>10% Steigung)
- **Rote Markierung**: Im Höhenprofil werden Abschnitte mit >10% Steigung/Gefälle mit rotem Hintergrundband und Prozentanzeige markiert (▲12% / ▼15%).
- **Lastenrad-kritisch**: Ab 10% Steigung mit 200kg Gesamtgewicht → unmenschlich. Visuelle Warnung direkt im Profil.

#### New Feature: Smart Info-Card (POI-Popups)
- **"Zugänglich für 1m Breite?"**: Jeder POI-Popup zeigt sofort eine farbcodierte Info-Card:
  - ✅ **JA** (grün) — `_cargo_access=yes`
  - ⛔ **NEIN** (rot) — `_cargo_access=no`
  - ⚠️ **UNBEKANNT** (gelb) — kein Tag vorhanden
- Kein Extra-Klick nötig — sofort sichtbar beim Öffnen des Popups.

#### Store Changes
- **useMapStore**: 3 neue States: `avoidHighways`, `avoidGravel`, `onlyAccessibleCharging` + Setter.
- Barriere-Filter aus RoutePanel-Checkbox → Header-Toggle (Zustand bleibt in useMapStore).

#### Geänderte Dateien
- `src/components/dashboard/HeaderBar.tsx` — Komplett-Rewrite: Lastenrad Power-Leiste + Quick-Action + Tooltips
- `src/store/useMapStore.ts` — 3 neue Filter-States
- `src/components/sidebar/RoutePanel.tsx` — avoidBarriers-Checkbox entfernt, Tooltips hinzugefügt, Barriere-Info-Badge
- `src/components/map/MapControls.tsx` — Komplett-Rewrite: Tooltips für alle Buttons + Kartenstil-Beschreibungen
- `src/components/map/ElevationProfile.tsx` — >10% Steigungs-Markierung (rot + Prozent)
- `src/components/map/MapView.tsx` — Smart Info-Card in POI-Popups (1m Zugänglichkeit)
- `VERSION` — 1.0.0
- `package.json` — 1.0.0
- `src/components/sidebar/Sidebar.tsx` — 1.0.0
- `src/lib/export.ts` — 1.0.0
- `src/lib/geocode.ts` — 1.0.0
- `CHANGELOG.md` — v1.0.0 Eintrag

---

## [0.9.0] — 2026-04-15

### Major — "The Productivity Layout": Dashboard-Architektur, Kontext-Aktionen, Dynamic Summary

Komplettes UI-Redesign: Weg vom Tab-System, hin zu einem professionellen Drei-Zonen-Dashboard. Alle Kernfunktionen sind jetzt sichtbar ohne Tab-Wechsel — Route, POIs, Session und Chat gleichzeitig erreichbar. Das Design folgt einem "Productivity Layout" mit optimiertem Workflow für die Reiseplanung.

#### Architecture Changes
- **Tab-System komplett eliminiert**: Die 5-Tab-Sidebar (Route, POIs, Session, Chat, Export) ist Geschichte. Stattdessen gibt es drei Zonen: Header, linke Sidebar, rechte Sidebar.
- **Drei-Zonen-Dashboard**: `DashboardLayout` ersetzt die alte Sidebar. Linke Sidebar (340px) für Route-Konfiguration, rechte Sidebar (320px) für POI-Suche und Analyse, Center für die Karte.
- **Neu: `src/components/dashboard/DashboardLayout.tsx`**: Haupt-Layout-Wrapper mit Header + zwei kollabierbaren Sidebars. Toggle-Buttons erscheinen wenn eine Sidebar geschlossen ist.
- **Neu: `src/components/dashboard/HeaderBar.tsx`**: 48px Header-Leiste mit RS-Logo + Versionsbadge, 8 POI-Category-Chips (inline, klickbar), Kartenstil-Switcher (5 Emoji-Buttons), Session-Code-Badge, Teilnehmerzahl, Verbindungsindikator und Settings-Icon.
- **Neu: `src/components/dashboard/LeftSidebar.tsx`**: Wrapper um den bestehenden `RoutePanel` — beinhält Start/Via/Ziel-Eingaben, Profil-Selector, Wegpunkt-Liste, Alternativrouten.
- **Neu: `src/components/dashboard/RightSidebar.tsx`**: Kombiniert dynamische Routenübersicht (Distanz, Steigung, Gefälle, Dauer) mit dem bestehenden `POIPanel` — POI-Suche, -Kategorien, -Radius und -Ergebnisse.
- **Neu: `src/components/dashboard/SettingsPanel.tsx`**: Slide-Over-Panel von rechts (380px) mit 4 Tabs: Sitzung (SessionPanel), Chat (ChatPanel), Export (ExportPanel), Karte (Terrain 3D, Hillshade, Kompass, Layer-Visibility). Schließt per Escape-Taste oder Klick außerhalb.

#### New Features
- **Dynamic Summary Bar**: Sticky-Leiste in der rechten Sidebar die permanent Distanz, Gesamthöhenmeter (↑/↓), ETA und Dauer anzeigt sobald eine Route berechnet ist.
- **POI Category Chips im Header**: 8 Hauptkategorien (Ladesäule, Restaurant, Café, Krankenhaus, Apotheke, Trinkwasser, Fahrradwerkstatt, Camping) als inline-klickbare Chips in der Header-Leiste. Aktivierung/deaktivierung ohne Seitenwechsel.
- **Kartenstil-Switcher im Header**: Kompakte Emoji-Buttons (🗺️🌙🛰️⛰️🌄) direkt in der Header-Leiste — kein Extra-Menü nötig.
- **Kollabierbare Sidebars**: Beide Sidebars können per Button ein-/ausgeklappt werden. Toggle-Buttons erscheinen am Rand der Karte wenn eine Sidebar geschlossen ist. Maximale Kartenfläche wenn beide Sidebars eingeklappt sind.
- **Contextual Action: "Hier Ladepunkte suchen"**: Rechtsklick auf der Karte zeigt jetzt "⚡ Hier Ladepunkte suchen" — fliegt zum Klickpunkt, aktiviert 'charging'-Kategorie, startet POI-Suche (10km Radius).
- **Contextual Action: "Umleitung planen"**: Rechtsklick auf der Karte zeigt jetzt "🔄 Umleitung planen" — fügt Via-Wegpunkt an Klickposition hinzu mit Reverse-Geocoding für den Namen.
- **Map Settings in SettingsPanel**: Terrain 3D, Hillshade, Sonnenaufgangs-Kompass und Layer-Visibility (Route, Alt-Routen, POIs, Wegpunkte) als Toggle-Switches im Settings-Panel organisiert.

#### Geänderte Dateien
- `src/app/page.tsx` — Dashboard-Layout statt Sidebar
- `src/components/dashboard/DashboardLayout.tsx` — NEU: Drei-Zonen-Layout
- `src/components/dashboard/HeaderBar.tsx` — NEU: Header mit POI-Chips + Kartenstil + Session
- `src/components/dashboard/LeftSidebar.tsx` — NEU: Route-Konfiguration
- `src/components/dashboard/RightSidebar.tsx` — NEU: POI + Dynamic Summary
- `src/components/dashboard/SettingsPanel.tsx` — NEU: Settings Slide-Over
- `src/components/map/MapView.tsx` — 2 neue Kontext-Menü-Einträge
- `VERSION` — 0.9.0
- `package.json` — 0.9.0
- `src/components/sidebar/Sidebar.tsx` — 0.9.0 (Fallback, noch vorhanden aber nicht mehr aktiv genutzt)
- `src/lib/export.ts` — 0.9.0
- `src/lib/geocode.ts` — 0.9.0
- `README.md` — v0.9.0 Badge, Versionshistorie

---

## [0.8.1] — 2026-04-15

### Patch — Center-Query Tile-Cache-Integration, Center-Radius-Filter, Brand-Regex-Konsolidierung, Result-Limit

Center-Queries (POI-Suche ohne Route, nur Kartenmitte) ignorierten den Tile-Cache komplett und trafen immer live auf Overpass. Bei jeder Kartenbewegung wurde eine neue Overpass-Query abgefeuert — ohne Debounce, ohne Dedup, ohne Cache. In dicht besiedelten Gebieten (Großstädte) returnierte eine einzige Query über 1000 POIs (1471 Elemente → 1102 POIs), was zu massiven 429-Rate-Limits und 30s Cooldowns führte. Vier kritische Fixes identifiziert und behoben.

#### Critical Fixes
- **C1: Center-Queries ignorierten Tile-Cache komplett**: `searchPOIsAggregated()` nutzte den Tile-Cache (Phase 0) nur für Routen-Suchen (`hasRoute && geometry`). Center-Suchen (`center && !hasRoute`) übersprangen Phase 0 und gingen direkt zu Overpass — bei jeder Suche, auch wenn die Tiles bereits gecacht waren. Ergebnisse: "0 tile-cache" in den Logs, every time a fresh Overpass hit.
  - **Fix**: Center-Queries generieren jetzt Tile-IDs aus `[[center.lon, center.lat]]` mit `centerRadius` als Padding. Tile-Cache wird als Phase 0 geprüft — identisch zum Route-Flow. Wenn alle Center-Tiles gecacht sind, wird Overpass komplett übersprungen.
- **C2: Kein Center-Radius-Filter für Tile-Cache POIs**: Tile-Cache enthält POIs aus dem gesamten 1°×1° Tile (bis zu ~110km Kantenlänge). Bei einer Center-Suche mit 10km Radius wurden alle POIs im gesamten Tile angezeigt — auch die 100km entfernten. Kein Filter entfernte die POIs jenseits des gewählten Suchradius.
  - **Fix**: Nach dem Laden der Tile-Cache POIs wird ein Distance-Filter angewendet: `poi.distance <= centerRadius`. POIs jenseits des Suchradius werden entfernt. Log: "Center radius filter: removed N POIs beyond Xkm".
- **C3: Brand-Queries erzeugten 60 separate Overpass-Filter**: `buildEnhancedChargingCenterQuery()` hatte 30 Marken × 2 Filter (brand + operator) = 60 einzelne `around:`-Filterzeilen. Bei jedem Center-Query mit Lade-Säulen-Kategorie wurde eine gigantische Overpass-Query mit 60+ Filtern generiert — langsam, fehleranfällig, und oft über dem Overpass-Speicherlimit.
  - **Fix**: Alle 30+ Marken in einen einzelnen Regex-Filter konsolidiert: `brand~"ELEN|Ionity|Tesla|Trugo|...",i`. Zwei Zeilen statt 60 (eine für brand, eine für operator). Gleiche Coverage, 1 Query, schnelleres Parsing, weniger Overpass-Load.
- **C4: Kein Result-Limit bei großen Suchradien**: Bei 100km Suchradius um ein Stadtzentrum konnten Overpass-Queries Tausende von Elementen zurückgeben (1471 beobachtet → 1102 nach Dedup). Diese Flut an POIs überlastete die UI, den Dedup-Algorithmus und die IndexedDB-Caching-Phase.
  - **Fix**: Dynamisches Output-Limit: `radius > 50km → 500`, sonst `2000`. Zusätzlich: Way-Suche (große Polygone) nur bei Radius ≤25km (vorher ≤25km für Enhanced Charging Query beibehalten, aber mit höherem Timeout für große Radii: 45s statt 25s).

#### Geänderte Dateien
- `src/lib/poi-aggregator.ts` — Tile-Cache für Center-Queries, Center-Radius-Filter, Result-Limit
- `VERSION` — 0.8.1
- `package.json` — 0.8.1
- `src/components/sidebar/Sidebar.tsx` — 0.8.1
- `src/lib/export.ts` — 0.8.1
- `src/lib/geocode.ts` — 0.8.1
- `README.md` — v0.8.1 Badge, Versionshistorie
- `CHANGELOG.md` — v0.8.1 Eintrag

---

## [0.8.0] — 2026-04-15

### Major — Tile-basierter POI-Cache: IndexedDB, 7-Tage-TTL, Instant Reload

Das POI-System speichert Overpass-Ergebnisse jetzt automatisch in einem 1°×1° Tile-Cache (IndexedDB). Bei wiederholten Suchen auf der gleichen oder ähnlichen Route werden POIs sofort aus dem Cache geladen — ohne Overpass-Query, ohne 10s Delay, ohne 429 Risk. Die Zielvorgabe: **<1s für 500km gecachte Route** (vs. 60-120s mit Live-Overpass).

#### New Features
- **1°×1° Tile-Cache (IndexedDB)**: Die Welt ist in Grad-Raster aufgeteilt. Nach jeder Overpass-Suche werden die Ergebnisse automatisch in ihre jeweiligen Tiles gespeichert. IndexedDB persistiert den Cache über Browser-Sessions hinweg — kein Datenverlust bei Page-Reload.
- **7-Tage TTL pro Tile**: Cached Tiles verfallen nach 7 Tagen und werden bei der nächsten Suche automatisch durch frische Overpass-Daten ersetzt. Garantiert aktuelle Daten ohne manuelle Cache-Verwaltung.
- **Intelligentes Tile-Merging**: Wenn ein Tile bereits Daten enthält (z.B. von einer vorherigen Suche mit anderen Kategorien), werden neue Overpass-Ergebnisse merged — keine Daten gehen verloren. Max 5000 Elemente pro Tile verhindern Speicher-Bloat.
- **Cache-First Architektur**: Bei jeder POI-Suche wird zuerst der Tile-Cache geprüft. Wenn ALLE Route-Tiles gecacht sind, wird Overpass komplett übersprungen (<1s Gesamtdauer). Bei teil-gecachten Routen werden nur die fehlenden Tiles per Overpass abgefragt.
- **Tile-Cache Stats in UI**: Die Stats-Bar zeigt jetzt violett "Cache: X" für gecachte POIs und "X/Y Tiles" für Cache-Hit-Rate. OSM/Lokal/Merge-Stats bleiben wie gewohnt.
- **Tile-Generator Skript**: `scripts/generate-poi-tiles.ts` ermöglicht die Vorgenerierung von statischen Tile-Dateien für beliebige Regionen. Optional — der Cache funktioniert auch ohne statische Tiles.

#### Architecture
- **`src/lib/poi-tiles.ts`** (NEU): Komplettes Tile-Cache-System mit IndexedDB. Enthält: `getIntersectingTileIds()` (Route→Tiles), `loadCachedPOIs()` (Batch-Read aus IndexedDB), `cacheOverpassResults()` (Write+Merge), `clearTileCache()`, `getCachedTileCount()`. SSR-sicher (IndexedDB nur im Browser).
- **`src/lib/poi-aggregator.ts` v3**: Tile-Cache als "Phase 0" vor Overpass-Queries. Neuer Statistik-Typ mit `tileCache`, `tileCacheHits`, `tileCacheMisses`. Alle Overpass-Elemente werden nach der Suche in Tiles gecacht.
- **`src/components/sidebar/POIPanel.tsx`**: Stats-Bar erweitert mit Cache-Badge (violett) und Tile-Hit-Rate.

#### Performance-Vergleich

| Szenario | v0.7.2 (Sektoren) | v0.8.0 (Tile-Cache) |
|---|---|---|
| Erste Suche, 500km | 60-120s (10-20 Overpass) | 60-120s (gleich, + Caching) |
| Wiederholte Suche, gleiche Route | 60-120s (erneut Overpass) | **<1s** (IndexedDB) |
| Wiederholte Suche, ähnliche Route | 60-120s (komplett neu) | **<30s** (nur neue Tiles) |
| 429 Rate-Limit | 30s Cooldown | **Kein Problem** (Cache) |
| Offline (gecacht) | 0 POIs | **Vollständig** (aus Cache) |

#### Geänderte Dateien
- `src/lib/poi-tiles.ts` — NEU: Tile-Cache System (IndexedDB, 7d TTL, Merge)
- `src/lib/poi-aggregator.ts` — v3: Tile-Cache Phase 0, Auto-Caching, erweiterte Stats
- `src/components/sidebar/POIPanel.tsx` — Cache-Badge, Tile-Hit-Rate in Stats-Bar
- `scripts/generate-poi-tiles.ts` — NEU: Tile-Vorgenerator für statische Assets
- `VERSION` — 0.8.0
- `package.json` — 0.8.0
- `src/components/sidebar/Sidebar.tsx` — 0.8.0
- `src/lib/export.ts` — 0.8.0
- `src/lib/geocode.ts` — 0.8.0

---

## [0.7.2] — 2026-04-14

### Patch — Korridor-Filter, Brand-Integration, overpass.ru entfernt, Category-Detection Fix

POI-Suche auf 587km Route zeigte "halbe Türkei" an Ladesäulen und Sektoren 2-11 fanden 0 POIs. Drei kritische Bugs identifiziert und behoben.

#### Critical Fixes
- **P1: Brand-Queries nutzten GESAMTE Routen-BBOX statt Korridor**: `buildBrandQueries()` berechnete eine BBox über die komplette 587km Route (~150.000 km2). Ergebnis: 121 POIs quer durch die Türkei, viele hunderte Kilometer von der Route entfernt. Overpass-Timeouts auf so großen Queries (26s Server-Timeout).
  - **Fix**: Brand-Queries komplett aus dem segmentierten Modus entfernt. Marken-Suche (brand/operator/supercharge) ist jetzt in JEDE Sektor-Query integriert über `buildCorridorPOIQuery()`. Einzelner Regex mit `|` (OR) — 3 extra Filter-Zeilen statt 60+.
- **P2: Sektoren fanden 0 POIs weil Brand-Tags fehlten**: Sektor-Queries suchten nur `amenity=charging_station`. In SE-Europa (Bosnien, Serbien, Türkei) sind viele Ladesäulen nur per `brand=Trugo` oder `operator=ZES` getaggt — OHNE `amenity=charging_station`. Diese wurden von Sektor-Queries komplett ignoriert. Nur die Full-BBOX Brand-Queries (Bug P1) fanden sie — aber ohne Korridor-Filter.
  - **Fix**: `buildCorridorPOIQuery()` enthält jetzt 30+ Marken (ELEN, Trugo, ZES, Ionity, Tesla, Shell, etc.) als `brand~"..."` und `operator~"..."` Regex-Filter. Jeder 50km-Sektor findet jetzt alle Ladesäulen — egal wie sie getaggt sind.
- **P3: Category-Detection klassifizierte ALLES als "charging"**: `processElements()` zwang jeden POI mit `brand` oder `operator` Tag zu `category='charging'` — auch Shell-Tankstellen (`amenity=fuel` + `brand=Shell`). Ergebnis: Tankstellen erschienen als Ladesäulen in den Ergebnissen.
  - **Fix**: 3-stufige Category-Detection: (1) `amenity=charging_station` oder `supercharge=yes` → definitiv charging. (2) Brand/Operator OHNE konfligierende Amenität → charging. (3) Standard `detectCategory()`. Konfligierende Amenitäten (fuel, restaurant, etc.) und Shops verhindern Falschzuordnung.
- **P4: overpass.ru consistently dead**: `overpass.openstreetmap.ru` antwortete jedes Mal mit `ERR_CONNECTION_TIMED_OUT`. Im Race verbrauchte es einen Slot und 25s Timeout pro Query — ohne jemals zu antworten.
  - **Fix**: Komplett aus `OVERPASS_ENDPOINTS` entfernt. 3 statt 4 Endpunkte im Race = weniger Rate-Limit-Stress.

#### New Features
- **Korridor-Distanzfilter**: Nach allen Overpass-Queries werden OSM-POIs gefiltert die mehr als 1.5x Korridor-Radius von der Route entfernt sind. Verhindert dass BBOX-Überschuss (Rechteck vs. Linie) POIs weit weg von der Route anzeigt. Log-Ausgabe: "Corridor filter: removed N POIs too far from route".

#### Architecture Changes
- **`overpass.ts`**: `ALL_CHARGING_BRANDS` Konstante (30+ Marken als Regex). `buildCorridorPOIQuery()` erweitert mit brand/operator/supercharge Filtern wenn `charging` Kategorie ausgewählt. `overpass.ru` entfernt. `QUERY_DELAY_MS` 6s → 10s (weniger 429s).
- **`poi-aggregator.ts`**: Full-BBOX Brand-Queries im segmentierten Modus entfernt. Korridor-Distanzfilter nach Overpass-Queries. Smart Category-Detection in `processElements()`.

#### Geänderte Dateien
- `src/lib/overpass.ts` — ALL_CHARGING_BRANDS, buildCorridorPOIQuery Brand-Erweiterung, overpass.ru entfernt, Delay 10s
- `src/lib/poi-aggregator.ts` — Brand-Queries entfernt, Korridor-Filter, Smart Category-Detection
- `VERSION` — 0.7.1
- `package.json` — 0.7.1
- `src/components/sidebar/Sidebar.tsx` — 0.7.1
- `src/lib/export.ts` — 0.7.1
- `src/lib/geocode.ts` — 0.7.1
- `README.md` — v0.7.1 Badge, Versionshistorie
- `CHANGELOG.md` — v0.7.1 Eintrag

---

## [0.7.0] — 2026-04-14

### Major — POI-Streaming: 500km Sektoren-Segmentierung, Endpoint-Racing, Result-Cache, Lastenrad-Check

Komplettes Overpass-System überarbeitet. POI-Suche funktioniert jetzt auf 500km+ Strecken ohne Timeout-Blackout. Die Segmentierung ist für den User komplett unsichtbar — die UI zeigt Echtzeit-Fortschritt.

#### Critical Fixes
- **O1: Overpass Endpunkt-Fallback verursachte 10+ min Timeout-Kaskade**: Jede Query probierte alle 4 Endpunkte SEQUENTIELL mit je 60s Timeout. Bei Ausfall der ersten 3 Endpunkte: 4x60s = 240s pro Query. Bei 5 Queries: 20+ Minuten Gesamt. Die v0.6.1 Lö sung (FIFO Queue + sequenzielle Queries) mach es noch SCHLIMM — statt parallelem Racing wurden Endpunkte nacheinander probiert.
  - **Fix**: `raceEndpoints()` — alle gesunden Endpunkte starten gleichzeitig. Erste erfolgreiche Antwort gewinnt (~1-3s), alle anderen werden per `AbortController` abgebrochen. 25s globaler Race-Timeout (statt 60s pro Endpunkt). `Promise.any`-Pattern mit manuellem Cleanup.
- **O2: `endpointRotationIdx` nie deklariert — TDZ-Crash im Strict Mode**: Variable wurde in `fetchOverpassImpl()` verwendet (Zeilen 111, 155, 181, 198, 227) aber nie mit `let` deklariert. In strict mode (ES Modules) wirft der Zugriff auf eine undeklarierte `let`-Variable vor ihrer Deklaration einen `ReferenceError`.
  - **Fix**: `let endpointRotationIdx = 0;` auf Module-Ebene deklariert.
- **O3: 500km BBox-Query zu gross für Overpass**: Eine einzige Bounding-Box über 500km Route erzeugte Queries die den Overpass-Speicherlimit überschritten oder nach 45s Server-Timeout abbrachen. Ergebnis: 0 POIs bei Langstrecken.
  - **Fix**: Routes >100km werden automatisch in 50km-Sektoren mit 5km Überlappung zerlegt. Jeder Sektor bekommt eine kleine, schnelle BBox-Query (~3s). 10 Sektoren x 6s Delay = ~60s Gesamt (vs. 10+ Minuten vorher).

#### New Features
- **500km Sektoren-Segmentierung**: Routes >100km → automatische Zerlegung in 50kmChunks mit 5km Overlap. Routes <=100km nutzen weiterhin Single-BBox (Fast-Path). Konfigurierbar: `SECTOR_LENGTH_KM`, `SECTOR_OVERLAP_KM`.
- **Endpoint-Racing (v4)**: Alle gesunden Endpunkte starten gleichzeitig. Schnellster gewinnt, Rest wird abgebrochen. Kein sequenzieller 60s-Fallback mehr.
- **Result-Cache (10min TTL)**: Identische Overpass-Queries liefern sofort Cached Result. Max 50 Einträge. Cache-Hits überspringen die FIFO-Queue komplett (sofortiges Ergebnis).
- **Such-Fortschrittsanzeige**: Echtzeit-Progress-Bar zeigt Sektor X/Y, POI-Zähler, Phase (Sektoren/Marken/Lokal/Dedup). Nutzer sieht dass die Suche aktiv läuft.
- **Abbrechen-Button (Stop)**: Suche kann jederzeit zwischen Sektoren abgebrochen werden. `AbortSignal` wird an den Aggregator weitergegeben.
- **Lastenrad-Zugänglichkeits-Check**: POIs werden auf `bicycle=yes/no`, `access=private/customers`, `maxwidth<1.0` geprüft. POIs mit `_cargo_access=no` werden in der UI mit rotem Badge markiert. Im Detail-Expand: grüner "Lastenrad zugänglich" / roter "Lastenrad NICHT zugänglich" Indikator.

#### Architecture
- **`overpass.ts` v4**: Komplett-Rewrite mit `raceEndpoints()` (paralleles Racing + AbortController), `resultCache` (In-Memory mit TTL), `executeWithRetry()` (Retry mit frischer Endpunkt-Liste). `clearOverpassCache()` zum manuellen Cache-Reset.
- **`poi-aggregator.ts` v2**: `splitRouteIntoSectors()` für 50km-Segmentierung. `AggregatorOptions` mit `onProgress` callback und `signal` (AbortSignal). `processElements()` Helper zentralisiert die Overpass-Response→POI Konvertierung. `checkCargoAccessibility()` prüft Lastenrad-Zugänglichkeit pro POI.
- **`SearchProgress` Typ**: Neue Interface in `types/index.ts` mit `phase`, `current`, `total`, `poisFound`, `message`.
- **`usePOIStore`**: Neuer State `searchProgress: SearchProgress | null` mit Setter.
- **POIPanel**: `SearchProgressBar` Komponente mit animiertem Fortschrittsbalken. Stop-Button zum Abbrechen. Sektor-Anzeige in Stats-Bar. Lastenrad-Zugänglichkeit Badges.

#### Geänderte Dateien
- `src/lib/overpass.ts` — Komplett-Rewrite v4: Endpoint-Racing, Result-Cache, endpointRotationIdx Fix
- `src/lib/poi-aggregator.ts` — Komplett-Rewrite v2: 50km-Sektoren, Progress, Abort, Cargo-Check
- `src/types/index.ts` — SearchProgress Interface
- `src/store/usePOIStore.ts` — searchProgress State
- `src/components/sidebar/POIPanel.tsx` — Progress-Bar, Stop-Button, Cargo-Badges, AbortSignal
- `VERSION` — 0.7.0
- `package.json` — 0.7.0
- `src/components/sidebar/Sidebar.tsx` — 0.7.0
- `src/lib/export.ts` — 0.7.0
- `src/lib/geocode.ts` — 0.7.0
- `README.md` — v0.7.0 Badge, 500km+ Support, Lastenrad-Check Features

---

## [0.6.3] — 2026-04-14

### Patch — Overpass Queue v3: Delays, Timeout, Endpoint Health

#### Critical Fixes
- **O3: 5s Delay zwischen Queries war definiert aber nie aufgerufen**: `QUERY_DELAY_MS = 5000` existierte im Code, aber `processQueue()` hatte kein `await sleep()` — Queries wurden sofort nacheinander gefeuert ohne Pause.
  - **Fix**: `processQueue()` hat jetzt `await sleep(QUERY_DELAY_MS)` zwischen Queries (erste Query ohne Delay).
- **O4: 30s Client-Timeout killt Queries die der Server noch bearbeitet**: Overpass Server-Timeout ist 45s, aber `AbortController` brach nach 30s ab. Query 1 schaffte es (kumi.systems), Query 2-5 timed aus weil der Server noch rechnete.
  - **Fix**: `TIMEOUT_MS` von 30s auf **60s** erhöht (45s Server + 15s Netzwerk-Puffer).
- **O5: Tote Endpoints werden endlos retryt**: `overpass.openstreetmap.ru` (immer `ERR_CONNECTION_TIMED_OUT`) und `overpass-api.de` (504) wurden bei JEDEM Retry wieder probiert. Bei Query 2-5 wurden jeweils 4 Endpoints × 3 Retries = 12 Versuche pro Query verschwendet.
  - **Fix**: Endpoint Health Tracking — jeder Endpoint hat `failCount`. Nach 2+ aufeinanderfolgenden Fehlern → 2min Cooldown (wird komplett übersprungen). Bei Erfolg → Reset. `overpass.ru` landet nach erstem Fail sofort in Cooldown.

#### Geänderte Dateien
- `src/lib/overpass.ts` — Queue v3: Inter-Query Delay aktiviert, 60s Timeout, Endpoint Health Tracking
- `VERSION` — 0.6.3
- `package.json` — 0.6.3
- `src/components/sidebar/Sidebar.tsx` — 0.6.3
- `src/lib/export.ts` — 0.6.3
- `src/lib/geocode.ts` — 0.6.3

---

## [0.6.2] — 2026-04-14

### Patch — README auf aktuellen Stand gebracht

#### Fixes
- **R1: README bei 0.4.3 stehengeblieben**: Versionsnummer, Badge und Versionshistorie-Tabelle zeigten 0.4.3 statt 0.6.1. Fehlende Versionen (0.5.0–0.6.1) nicht in Tabelle aufgeführt.
  - **Fix**: README komplett aktualisiert: Titel, Badge, Versionshistorie-Tabelle (7 fehlende Einträge ergänzt). Feature-Tabellen aktualisiert: 8 Profile (Cargo), 19 Kategorien, Smart Dedup, 8 EV-Datensätze (97 Stationen), Cargo-Sicherheitsanalyse, FIFO Queue Rate-Limit Schutz.

#### Geänderte Dateien
- `README.md` — Version 0.6.2, Badge, Versionshistorie, Feature-Tabellen
- `VERSION` — 0.6.2
- `package.json` — 0.6.2
- `src/components/sidebar/Sidebar.tsx` — 0.6.2
- `src/lib/export.ts` — 0.6.2
- `src/lib/geocode.ts` — 0.6.2

---

## [0.6.1] — 2026-04-14

### Patch — Overpass Rate-Limit Fix: FIFO Queue ersetzt Parallel Burst

#### Critical Fixes
- **O1: 24 parallele Overpass-Requests verursachten 429 Storm**: `Promise.allSettled` feuerte bis zu 6 Queries gleichzeitig, jeder mit `Promise.race()` gegen alle 4 Endpunkte = 24 gleichzeitige HTTP-Requests. Overpass-API (Limit: ~2 req/min pro Instanz) antwortete sofort mit 429. Sequential-Fallback kaskadierte durch ALLE Endpunkte und erzeugte weitere Requests.
  - **Fix**: Komplett-Neu: FIFO Queue (modelliert nach `elevation.ts` v2 Pattern). Alle `fetchOverpass()` Aufrufe werden streng sequenziell abgearbeitet. 5s Delay zwischen Queries. Endpoint-Rotation verteilt Load gleichmäßig auf alle 4 Endpunkte. 429-Erkennung mit globalem 30s Cooldown + exponentiellem Backoff (8s → 16s). Max 3 Retries pro Query. 30s Timeout pro Request.
- **O2: poi-aggregator parallel dispatch**: `Promise.allSettled(queries.map(...))` startete alle Queries parallel statt sequenziell.
  - **Fix**: Sequenzielle `for`-Schleife ersetzt `Promise.allSettled`. Graceful Degradation bleibt erhalten.

#### Geänderte Dateien
- `src/lib/overpass.ts` — Komplett-Rewrite: FIFO Queue, 429-Erkennung, Endpoint-Rotation
- `src/lib/poi-aggregator.ts` — Promise.allSettled → sequenzielle For-Schleife
- `VERSION` — 0.6.1
- `package.json` — 0.6.1
- `src/components/sidebar/Sidebar.tsx` — 0.6.1
- `src/lib/export.ts` — 0.6.1
- `src/lib/geocode.ts` — 0.6.1

---

## [0.6.0] — 2026-04-14

### Major — Cargo Bike (Lastenrad) Routing: Barriere-Check, Oberflächen-Analyse, Steigungs-Sicherheit, Griechenland-Expansion

CTO-Audit: Kompletter Code zerpflückt und auf 1m+ Lastenrad-Physik ausgerichtet. Keine "Fahrradwege" mehr — "Lastenrad-Trassen".

#### Critical Fixes
- **B1: Kein Lastenrad-Profil**: 7 Profile (Rennrad, Trekking, MTB, Safety, Auto×2, Walk) — kein Profil für 1m+ breite Lastenräder. BRouter bietet kein natives Cargo-Profil.
  - **Fix**: Neues `cargo` Profil hinzugefügt (first in list). Nutzt `trekking` als BRouter-Basis (bestes Breitenprofil), mit `safety` als sichere Alternative. 3 Routen-Varianten: shortest/fastest/safest.
- **B2: Barriere-Check komplett fehlte**: `avoidBarriers` Parameter existierte in `calculateRoutes()` wurde aber **NIEMALS verwendet**. cycle_barrier, kissing_gate, bollard — alles ignoriert. Ein 1m Lastenrad passt durch kaum eine Standard-Umlaufsperre.
  - **Fix**: Neue POI-Kategorien `barrier` (cycle_barrier, kissing_gate, stile, gate, lift_gate), `narrow` (bollard, block). Overpass-Query `buildCargoSafetyQuery()` sucht aktiv nach Barrieren entlang der Route. Neue Kategorie-Tags in `CATEGORY_EXTRA_TAGS`.
- **B3: Oberflächen-Analyse fehlte**: surface/smoothness/tracktype wurden komplett ignoriert. Schweres Lastenrad braucht stabilen Untergrund — sand, mud, soft_gravel sind unpassierbar.
  - **Fix**: `SURFACE_RATING` Tabelle mit 18 Oberflächen-Typen, qualitativer Bewertung (1-5) und Farbcodierung für Lastenrad. surface_alert POI-Kategorie. Inline in routing.ts für UI-Integration.
- **B4: Steigungs-Sicherheit nicht Lastenrad-spezifisch**: Grad-Schwellen (2/5/8%) für Rennrad, nicht für 1m+ Lastenrad. Ab 8% bergauf mit 200kg Gesamtgewicht → unmenschlich.
  - **Fix**: `CARGO_GRADE_LIMITS` (4/6/10/15%) als konstante Schwellen. `gradeColor(grade, cargoMode)` mit Cargo-spezifischen Schwellen. `analyzeCargoGradeSafety()` generiert Warnungen: moderate (4-6%), difficult (6-10%), extreme (>10%).
- **B5: Griechenland fehlte komplett**: 0 lokale Ladesäulen in Griechenland. Kroatien→Türkei Route durchquiert Griechenland (Ignoumenitsa→Alexandroupoli), aber keine Backup-Daten.
  - **Fix**: 25 DEI/Hellevi-Stationen: Athen (3), A1 Korridor (3), Thessaloniki (2), Egnatia Odos E90 (4), Patra/Corinth (2), A7 Süd (2), Turkey-Grenze (3), Kreta (1), Ionien (3). Lückenlose Abdeckung Kroatien→Türkei über 8 Länder.

#### Architecture
- **3 neue POI-Kategorien**: `barrier` (🚧), `narrow` (⚠️), `surface_alert` (🟫) — Cargo-Bike-spezifische Warnungen
- **buildCargoSafetyQuery()**: Dedizierte Overpass-Query für Barriere-Erkennung (cycle_barrier, kissing_gate, stile, bollard + width<1.2m Wege)
- **8 lokale EV-Datensätze** (vorher 7): +greece-charging.ts

#### Geänderte Dateien
- `src/types/index.ts` — 3 neue POI-Kategorien + Config
- `src/lib/routing.ts` — Lastenrad-Profil, CARGO_GRADE_LIMITS, SURFACE_RATING
- `src/lib/elevation.ts` — gradeColor cargoMode, analyzeCargoGradeSafety()
- `src/lib/overpass.ts` — barrier/narrow/surface_alert Tags, buildCargoSafetyQuery()
- `src/lib/poi-local.ts` — greece-charging import + registry
- `src/lib/poi-dedup.ts` — barrier/narrow/surface_alert Radii
- `src/data/poi/greece-charging.ts` — NEU: 25 DEI Stationen

---

## [0.5.9] — 2026-04-14

### Critical — POI Coverage Fix: BBox Queries + Turkish Station Expansion

POI-Suche fand zu wenige Ladesäulen, besonders in der Türkei. 200km Strecke ohne einzige Ladesäule — obwohl Trugo/ZES hunderte Stationen betreiben. Ursache: Point-Sampling bei 50-80km Abstand deckte nur ~6% des Korridors ab. BBox-Queries garantieren jetzt 100% Abdeckung.

#### Critical Fixes
- **P1: Korridor-Sampling deckte nur 6%**: Bei 200km Strecke mit 50-80km Sampling-Intervall landeten nur 2-3 `around:` Kreise auf der Route. Die Lücken zwischen den Kreisen wurden komplett ignoriert — eine Ladesäule 40km vom nächsten Sample-Punkt wurde nicht gefunden.
  - **Fix**: Alle POI-Queries (Charging, Brands, General) nutzen jetzt Bounding Box (`south,west,north,east`) statt Point-Sampling. Die BBox umschließt die gesamte Route mit Korridor-Padding — 100% Abdeckung, 0% Lücken.
- **P2: Brand-Queries mit case-sensitiven Tags**: Overpass `brand="Trugo"` findet `brand="trugo"` oder `brand="TRUGO"` NICHT. Türkische OSM-Mapper nutzen inkonsistente Groß-/Kleinschreibung.
  - **Fix**: Brand-Queries nutzen jetzt `~"Trugo",i` (case-insensitive Regex). Findet alle Varianten unabhängig von Schreibweise.
- **P3: Türkische lokale Daten zu dünn**: Nur 20 Stationen in 6 Großstädten. Dazwischen (D100 Istanbul→Ankara, D400 Izmir→Antalya) gab es keine lokalen Backup-Stationen.
  - **Fix**: 35 neue Stationen auf allen wichtigen Autobahnkorridoren (D100, D400, D200, D300, D330, D650, D010). Gesamt: 55 türkische Stationen in 30+ Städten.

#### Geänderte Dateien
- `src/lib/poi-aggregator.ts` — BBox statt Point-Sampling für Base-Charging + Brand-Queries, Case-Insensitive Regex, computeRouteBbox() Helper
- `src/lib/overpass.ts` — BBox statt Point-Sampling für buildCorridorPOIQuery (alle Kategorien)
- `src/data/poi/turkey-charging.ts` — 35 neue Stationen (+175%): D100, D400, D200, D300, D330, D650, D010, Schwarzmeer, Südostanatolien
- `VERSION` — 0.5.9
- `package.json` — 0.5.9
- `src/components/sidebar/Sidebar.tsx` — 0.5.9
- `src/lib/export.ts` — 0.5.9
- `src/lib/geocode.ts` — 0.5.9

---

## [0.5.8] — 2026-04-14

### Critical — Elevation 429 Fix (Final), Overpass Parallel Failover, CTO Audit

Vollständiger CTO-Audit des gesamten Code-Stacks. Elevation 429-Problem endgültig gelöst (Mutex war todbrotten, Queue-System implementiert). Overpass-Failover von sequentiell (75s+) auf parallel (erste Antwort gewinnt) umgestellt.

#### Critical Fixes
- **E1: Elevation Mutex komplett broken**: Der Promise-basierte Mutex in `elevation.ts` hatte eine Race-Condition: wenn Reacts Effect-Cleanup `abort()` aufruft, wurde das alte Fetch asynchron abgebrochen, aber das neue Fetch konnte trotzdem passieren weil die Mutex-Freigabe und die Promise-Auflösung in der falschen Reihenfolge passierten. Symptom: `batch 0` und `batch 100` liefen parallel und bombadierten die API mit 429s.
  - **Fix**: Komplett-Neu: FIFO Queue ersetzt den Mutex. Alle `fetchElevation()` Aufrufe werden in eine Warteschlange gestellt und streng sequenziell abgearbeitet. Keine parallelen API-Calls mehr, unabhängig davon wie schnell React Effects feuern.
- **E2: Elevation Dedup fehlte**: Gleiche Route → mehrere Fetches mit identischem Geometry → identische API-Calls. PeerSync sendet Route-Update, Effect re-fired mit neuer Geometry-Referenz (aber selben Daten) → redundanter API-Call.
  - **Fix**: `geometryHash()` berechnet stabilen Hash aus Geometry-Daten. Vor jedem Fetch wird der Cache geprüft — gleiche Geometrie liefert sofort Cached Result. Cache-TTL: 60s, max 5 Einträge.
- **E3: Kein Debounce bei PeerSync Route-Updates**: PeerSync sendet `waypoints` → `route` → `alt-routes` in schneller Folge. Jedes Update löst den Elevation-Effect aus, was zu 3 rapid-fire Fetches führte (2 abgebrochen, 1 aktiv).
  - **Fix**: 800ms Debounce im ElevationProfile-Effect. Erst nach 800ms ohne erneute Route-Änderung wird der Fetch gestartet. Verhindert die meisten redundanten API-Calls.

#### High Fixes
- **H1: Overpass Sequential Failover zu langsam**: `fetchOverpass()` probierte 4 Endpunkte sequentiell mit je 25s Timeout. Bei Ausfall der ersten 3 Endpunkte dauerte es 75s+ bevor der 4. probiert wurde. Für POI-Suche mit 6+ Queries unbrauchbar.
  - **Fix**: `Promise.race()` — alle 4 Endpunkte gleichzeitig. Erste erfolgreiche Antwort gewinnt, alle anderen werden per `AbortController` abgebrochen. Fallback auf sequentiel nur wenn alle parallelen Requests fehlschlagen.
- **H2: Batch-Größe zu groß**: MAX_POINTS=200 erzeugte 2 Batches à 100 Punkte. Bei 429 auf Batch 1 wurden beide Batches gelöscht → 0 Ergebnisse.
  - **Fix**: MAX_POINTS auf 150 reduziert → 2 Batches à 100 und 1 Batch à 50. Wenn Batch 1 scheitert, hat Batch 2 noch Chance.
- **H3: 429 Cooldown zu kurz**: 15s Cooldown reichte nicht immer aus — Open-Meteo kann längere Sperrzeiten haben.
  - **Fix**: Cooldown auf 20s erhöht.

#### Code Quality
- **Q1: Doppelter ElevPoint-Type**: `ElevPoint` Interface war sowohl in `elevation.ts` als auch in `useElevationStore.ts` definiert.
  - **Fix**: `useElevationStore.ts` importiert jetzt `ElevPoint` aus `elevation.ts` statt eigenen Duplikat.
- **Q2: Production Console-Logs**: `poi-dedup.ts` loggte Dedup-Stats auch in Produktion.
  - **Fix**: Gated hinter `process.env.NODE_ENV !== 'production'`.

#### Geänderte Dateien
- `src/lib/elevation.ts` — Komplett-Rewrite: FIFO Queue, Dedup-Cache, Queue-Processor, Batch-Reduzierung
- `src/components/map/ElevationProfile.tsx` — 800ms Debounce, 30s Retry-Timer, AbortController nach Debounce
- `src/lib/overpass.ts` — `Promise.race` für paralleles Failover, sequentieller Fallback
- `src/store/useElevationStore.ts` — ElevPoint Import statt lokaler Duplikation
- `src/lib/poi-dedup.ts` — Production-Log Gating
- `VERSION` — 0.5.8
- `package.json` — 0.5.8
- `src/components/sidebar/Sidebar.tsx` — 0.5.8
- `src/lib/export.ts` — 0.5.8
- `src/lib/geocode.ts` — 0.5.8

---

## [0.6.0] — 2026-04-14

### Major — Deep Audit & Fix: POI Maximization, AbortController, Transit Countries

Tiefgehender Audit des gesamten Code-Stacks mit Fokus auf maximale Ladesäulen-Abdeckung für die Kroatien→Türkei Route. 31 Issues identifiziert, alle Critical + High + key Medium Issues gefixt.

#### Critical Fixes
- **C1: Center-Suche kaputt (lng→lon)**: POIPanel erstellte Center-Objekt mit `lng` statt `lon`. Alle POI-Suchen ohne Route (nur Kartenmitte) hatten `undefined` Longitude → Suche ergab 0 Ergebnisse.
- **C2: Bulgarien Plovdiv falsche Koordinaten**: EVN Plovdiv Station hatte Sofia-Koordinaten (42.6975, 23.3260) statt Plovdiv (42.1354, 24.7453). Plovdiv-Ladesäule war in Wirklichkeit ein Duplikat von Sofia Centrum (350m Abstand).
- **C3: Brand-Query Trunkation (120→360)**: Mit 24 Marken × 3 Filter × 6 Sample Points = 432 Filter, wurden bei Limit 120 türkische und serbische Marken abgeschnitten. Limit auf 360 erhöht — keine truncation mehr für 25 Sample Points.
- **C4: Korridor-Sampling zu sparse für Langstrecke**: Bei 2000km Routen ergab `totalDist / 6` ein 333km Interval → nur 7 Sample Points → 0,7% Korridor-Abdeckung. Jetzt: dynamisch berechnet, max. 80km Interval → min. 25 Sample Points → volles Coverage.
- **C5: AbortController für POI-Suche**: Doppelte Suchen überschrieben sich gegenseitig. Neue Suche bricht jetzt vorherige per `AbortController` ab.
- **C6: AbortController für Routenberechnung**: Mehrfache "Berechnen"-Klicks erzeugten parallele BRouter-Requests. Alle außer dem letzten wurden ignoriert. Jetzt per `AbortSignal` abgebrochen.

#### High Fixes
- **H7: Overpass Result-Limit 500→2000**: Standard-Corridor-Queries truncierten bei 500 Elementen. Istanbul-Region hat hunderte Ladesäulen → viele fielen weg. Alle Queries jetzt auf 2000.
- **H8: 3 neue Transit-Länder (22 Ladesäulen)**: Bosnien & Herzegowina (8), Montenegro (6), Nordmazedonien (8) hinzugefügt. Gesamt: 72 lokale Stationen in 7 Ländern — lückenlose Abdeckung Kroatien→Türkei.
- **H9: Way-Suche nur bei kleinem Radius**: Enhanced Charging Query suchte Ways (große Polygone) auch bei 50km Korridor-Radius → Overpass Timeout. Jetzt nur bei ≤10km.
- **H6: Dead Code entfernt**: Unbenutzter `generateId`-Import aus poi-aggregator.ts entfernt.

#### Medium Fixes
- **M4: Sightseeing-Tags erweitert**: `tourism=gallery`, `tourism=viewpoint`, `historic=castle/ruins/fort/archaeological_site` für Balkan-Region hinzugefügt.
- **M5: Lokaler POI-Korridor-Filter verdoppelt**: Sampling von 100 auf 200 Punkte — bessere Coverage bei dichter Routengeometrie.
- **Overpass Timeout erhöht**: Bis zu 180s für Enhanced Charging Query (vorher 90s) — langsame Queries auf 25+ Sample Points werden nicht mehr abgeschnitten.

#### POI-System — Maximale Ladesäulen-Abdeckung
Vor dem Audit:
- 50 lokale Stationen in 4 Ländern
- Korridor-Sampling: 333km bei 2000km → 0,7% Coverage
- Brand-Limit: 120 (türkische Marken abgeschnitten)
- Overpass-Limit: 500 (viele Ladesäulen invisible)
- Kein Abort (stale Results)

Nach dem Audit:
- **72 lokale Stationen in 7 Ländern** (+44% Coverage)
- **Korridor-Sampling: 80km max → min. 25 Points → 100% Coverage**
- **Brand-Limit: 360** (alle Marken drin)
- **Overpass-Limit: 2000** (keine Truncation)
- **AbortController** auf POI + Routing

#### New Files
- `src/data/poi/bosnia-charging.ts` — 8 ELEN/JP EP/City Charge Stationen (Sarajevo, Zenica, Mostar, Banja Luka, Tuzla, Bijeljina)
- `src/data/poi/montenegro-charging.ts` — 6 CGES/Greenway/ELEN Stationen (Podgorica, Nikšić, Budva, Kotor, Herceg Novi)
- `src/data/poi/macedonia-charging.ts` — 8 EVN/MEPSO Stationen (Skopje, Bitola, Ohrid, Tetovo, Kumanovo, Štip)

#### Geänderte Dateien
- `src/lib/poi-aggregator.ts` — Sampling-Interval capped, Way-Guard, Brand-Limit 360, Overpass-Limit 2000, unused import removed
- `src/lib/overpass.ts` — Sampling-Interval capped, Sightseeing-Tags erweitert, Overpass-Limit 2000
- `src/lib/routing.ts` — AbortSignal-Parameter
- `src/components/sidebar/POIPanel.tsx` — lng→lon Fix, AbortController für Suche
- `src/components/sidebar/RoutePanel.tsx` — AbortController für Routing
- `src/lib/poi-local.ts` — 3 neue Transit-Länder importiert + registriert
- `src/data/poi/bulgaria-charging.ts` — Plovdiv Koordinaten korrigiert

---

## [0.5.1] — 2026-04-14

### Patch — Code-Dedup, Parallel Queries, Graceful Degradation

#### Architecture
- **formatPOIDetails() extrahiert**: 150+ Zeilen POI-Formatierungs-Code aus MapView.tsx in separates Modul `src/lib/poiFormatter.ts` ausgelagert. MapView um 153 Zeilen reduziert.
- **sampleRoute() dedupliziert**: War in `overpass.ts` und `poi-aggregator.ts` dupliziert → zentrale Funktion in `utils.ts`. Wird jetzt von beiden Modulen importiert.
- **Version zentralisiert**: `next.config.ts` liest `VERSION`-Datei und injiziert `NEXT_PUBLIC_APP_VERSION` als Env-Variable. Alle Dateien nutzen `process.env.NEXT_PUBLIC_APP_VERSION` statt hartcodierte Fallbacks. `package.json` Version synchronisiert.

#### POI Aggregator — Parallel Queries & Graceful Degradation
- **Overpass Queries parallel statt seriell**: Einzelner riesiger Query gesplitet in 2 parallele Queries (Base + Brand/Operator) bei Ladesäulen-Suche. Verhindert Timeouts bei langen Routen (Kroatien→Türkei).
- **Promise.allSettled statt Promise.all**: Wenn ein Overpass-Query fehlschlägt (Timeout, 500, leer), liefern die anderen trotzdem Ergebnisse. Graceful Degradation statt Totalausfall.
- **Cross-Query Deduplication**: `seenIds` Set verhindert Dubletten wenn Base- und Brand-Query denselben OSM-Node zurückgeben.
- **Smarter Route Sampling**: Bei langen Routen wird das Sampling-Interval dynamisch berechnet (Gesamtdistanz / 6), um max. ~6 Overpass-Around-Queries zu generieren. Verhindert Überlastung bei 2000km Routen.

#### POI Deduplication — Lokale Daten Verbessert
- **Erweiterter Radius für lokale Daten**: Wenn ein POI aus lokaler Quelle stammt (ungefähre Koordinaten), wird Dedup-Radius auf 200m statt 50m erweitert. Verhindert dass echte OSM-Dubletten nicht erkannt werden.
- **Interne Marker aufgeräumt**: `_source_a`, `_source_b`, `_local_dataset` Marker werden vor dem Merge entfernt — kein Datenmüll in merged Tags.

#### Security
- **generateId() mit crypto.randomUUID()**: Nutzt jetzt `crypto.randomUUID()` statt `Date.now()-Math.random()`. Fallback auf `crypto.getRandomValues()` in non-secure Contexts.
- **generateParticipantColor() mit crypto**: `Math.random()` für Farb-Auswahl ersetzt durch `crypto.getRandomValues()`.

#### Geänderte Dateien
- `src/lib/poiFormatter.ts` — NEU: formatPOIDetails() aus MapView ausgelagert
- `src/lib/poi-aggregator.ts` — Parallel Queries, Promise.allSettled, Cross-Query Dedup, dynamisches Sampling
- `src/lib/poi-dedup.ts` — 200m Radius für lokale Daten, Marker-Cleanup
- `src/lib/overpass.ts` — sampleRoute() entfernt (jetzt in utils.ts)
- `src/lib/utils.ts` — sampleRoute() zentral, generateId() mit crypto
- `src/lib/session.ts` — generateParticipantColor() mit crypto
- `src/lib/export.ts` — Version von Env-Variable
- `src/lib/geocode.ts` — User-Agent Version von Env-Variable
- `src/components/map/MapView.tsx` — formatPOIDetails importiert statt inline
- `src/components/sidebar/Sidebar.tsx` — Version von Env-Variable
- `next.config.ts` — VERSION-Datei als Env-Variable injiziert
- `package.json` — Version 0.5.1
- `VERSION` — 0.5.1

---

## [0.5.0] — 2026-04-14

### Major — POI Aggregator: Multi-Source, Smart Dedup, Local Data

#### New Features
- **POI Aggregator**: Modulares System zum Suchen und Zusammenführen von POI-Daten aus mehreren Quellen (OSM + lokale Dateien). Keine API-Keys nötig.
- **Multi-Query Overpass für Ladesäulen**: Enhanced Charging Query sucht nicht nur `amenity=charging_station`, sondern zusätzlich nach `supercharge=yes`, `brand=...` und `operator=...` für 30+ Netzwerke in Südosteuropa:
  - **Kroatien**: ELEN, Petrol, INA, MOL, Greenway, e-Mobi, AMZS
  - **Türkei**: Trugo (Togg), ZES, Aygaz, Shell, E-Sharj, Voltrun, ERFU
  - **Serbien**: NIS (Gazprom Neft), Energopro, Merkur
  - **Bulgarien**: EVN, E ON, OmV, Shell Recharge
  - **Europaweit**: Ionity, Tesla, ABB, Schneider
- **Smart Deduplication**: Union-Find Algorithmus mit geografischem Bucketing für O(n) Performance. Erkennt Dubletten anhand variablen Radius-Abgleichs (Ladesäulen: 50m, Parkplätze: 50m, Krankenhäuser: 200m). OSM-Daten haben Priorität, lokale Daten füllen Lücken auf.
- **Lokale POI-Datensätze (Offline-Backup)**: 4 statische Datensätze mit zusammen 50 Ladesäulen:
  - Kroatien ELEN (12 Stationen): Zagreb, Split, Rijeka, Osijek, Zadar, Pula, Dubrovnik, Šibenik, Karlovac, Slavonski Brod, Varaždin, Vukovar
  - Türkei Trugo/ZES (20 Stationen): Istanbul (4), Ankara (2), Izmir (2), Bursa (2), Antalya (2), Afyon, Burdur, Konya, Aksaray, Edirne, Tekirdağ
  - Serbien NIS/e-Mobi (8 Stationen): Belgrad, Novi Sad, Niš, Subotica, Čačak, Kragujevac, Zlatibor, Vršac
  - Bulgarien EVN/Shell (10 Stationen): Sofia (2), Plovdiv, Varna, Burgas, Stara Zagora, Veliko Tarnovo, Ruse, Blagoevgrad, Pernik
- **Aggregator Stats Bar**: Zeigt nach der Suche die Statistik: `OSM: X | Lokal: Y | Merge: Z` mit aktiven Quellen
- **Source Badges**: Jeder POI zeigt seine Datenquelle: "Lokal" (grün) für statische Daten, "Merge" (amber) für zusammengeführte Datensätze
- **Local Data Toggle**: Button zum Ein-/Ausschalten lokaler Datensätze (Standard: AN)

#### New Files
- `src/lib/poi-aggregator.ts` — Haupt-Aggregator: Orchestrat OSM + lokale Quellen + Dedup
- `src/lib/poi-dedup.ts` — Smart Deduplication: Union-Find + geografisches Bucketing
- `src/lib/poi-local.ts` — Lokaler POI Data Loader mit Registry-System
- `src/data/poi/croatia-charging.ts` — ELEN Kroatien Datensatz (12 Stationen)
- `src/data/poi/turkey-charging.ts` — Trugo/ZES Türkei Datensatz (20 Stationen)
- `src/data/poi/serbia-charging.ts` — NIS Serbien Datensatz (8 Stationen)
- `src/data/poi/bulgaria-charging.ts` — EVN/Shell Bulgarien Datensatz (10 Stationen)

#### Changed Files
- `src/types/index.ts` — POISource Type, POI.source Feld
- `src/components/sidebar/POIPanel.tsx` — Aggregator-Integration, Stats Bar, Source Badges, Local Toggle

---

## [0.4.4] — 2026-04-14

### Patch — UI Fixes: POI Detail-Expand, Overlay Safety

#### New Features
- **POI Detail-Expand**: Jeder POI in der Sidebar-Liste ist jetzt anklickbar. Ein Klick klappt ein umfangreiches Detail-Panel auf mit ALLEN verfügbaren Overpass-Tag-Daten — dieselben Infos wie im Karten-Popup:
  - **Ladesäulen**: Alle Steckertypen (T2, CCS, CHAdeMO, Schuko, CEE, Tesla) mit Leistung, Anzahl, Kabel-Info; Gesamt-Leistung; Bezahlung (Kontaktlos, App, Karte, Bargeld, Auth); Fahrrad/Auto; Kapazität; Gebühr
  - **Restaurant/Café**: Küche, Diät (Vegan, Vegetarisch, Glutenfrei, Halal, Koscher), Mitnehmen, Außengastronomie, Preisklasse, Lieferung, Bezahlung
  - **Tankstelle**: Marke, Kraftstoffarten (Diesel, E5, E10, Autogas, AdBlue)
  - **Camping**: Zelte, Wohnwagen, Sanitär, Dusche, Waschsalon, Typ, Sterne, Gebühr
  - **Hotel/Hostel**: Zimmer, Betten, Preisklasse, Sterne, Gebühr
  - **Parkplatz**: Typ (Fläche, Tiefgarage, Parkhaus, etc.), Gebühren, Kapazität, Bewacht, Max. Aufenthaltsdauer
  - **Krankenhaus**: Notaufnahme, Fachbereich, Notruf-Telefon
  - **Universal für ALLE**: Betreiber, Öffnungszeiten, Telefon, Mobil, Website, E-Mail, Rollstuhl, Toiletten, WLAN, Rauchen, Hunde, Vollständige Adresse (Straße, PLZ, Ort, Bundesland, Land), Wikipedia, Wikidata
  - **"Auf Karte" Button**: Springt direkt zum POI mit Fly-To-Animation
  - ChevronDown/ChevronUp Icons zeigen Expand-Status
- **Sidebar Overflow Safety**: `overflow-hidden` auf allen TabsContent-Elementen verhindert dass Sidebar-Content auf die Karte ragt

#### Fixes
- **Sidebar overflow-hidden**: TabsContent-Elemente haben jetzt explizit `overflow-hidden` als Safety-Margin gegen Text-Overflow aus der Sidebar

#### Geänderte Dateien
- `src/components/sidebar/POIPanel.tsx` — Komplett-Rewrite: POIDetailExpanded Komponente mit allen Overpass-Tags, Expand/Collapse, "Auf Karte" Button
- `src/components/sidebar/Sidebar.tsx` — overflow-hidden auf TabsContent, VERSION → 0.4.4
- `VERSION` — 0.4.4

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
