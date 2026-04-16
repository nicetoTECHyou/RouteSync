<div align="center">

# RouteSync

### Professional Cargo Bike Navigation

**Type 2 AC Charging — 1m Width Verification — Southeast Europe**

**RouteSync v1.5.6**

<br>

[![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=next.js&logoColor=white)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.8-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![MapLibre](https://img.shields.io/badge/MapLibre_GL-5.22-1AAC71?logo=maplibre&logoColor=white)](https://maplibre.org)
[![Zustand](https://img.shields.io/badge/Zustand-5-764ABC)](https://github.com/pmndrs/zustand)
[![Version](https://img.shields.io/badge/Version-1.5.6-10B981)](./CHANGELOG.md)

<br>

<img src="https://img.shields.io/badge/Cargo_Bike_Focused-1m_Breite-3B82F6?style=flat-square" /> &nbsp;
<img src="https://img.shields.io/badge/Typ_2_AC_Charging-15km_Search-EF4444?style=flat-square" /> &nbsp;
<img src="https://img.shields.io/badge/8_Laender-97_Stationen-10B981?style=flat-square" /> &nbsp;
<img src="https://img.shields.io/badge/API_Key_n%C3%B6tig-NEIN-success?style=flat-square" />

</div>

---

## Cargo Bike Navigation — Built for 1m Width

RouteSync is a professional-grade route planner specifically engineered for cargo bike world travel through Southeast Europe. The primary focus is on verifying route accessibility for bicycles wider than 1 meter — the critical dimension for loaded cargo bikes weighing 80-200kg. Every routing decision, POI filter, and charging station query is optimized for the physical constraints of heavy cargo bikes on Balkan roads.

The application prioritizes **Type 2 AC charging** (not DC/CCS/CHAdeMO) because cargo bikes typically use AC-compatible onboard chargers in the 500-2000W range. The Quick-Action button in the header performs a dedicated Overpass API query for `socket:type2=yes` within a 15km dynamic bounding box, filtering out pure DC fast chargers that are irrelevant for cargo bike charging needs.

### 1m Width Verification Technology

RouteSync actively queries the OpenStreetMap Overpass API for physical obstacles that block cargo bikes wider than 100cm. The barrier detection system identifies:

| Obstacle Type | OSM Tag | Cargo Bike Impact |
|---------------|---------|-------------------|
| **Cycle Barrier (Umlaufsperre)** | `barrier=cycle_barrier` | Standard gap is ~90cm — most cargo bikes CANNOT pass |
| **Kissing Gate** | `barrier=kissing_gate` | V-shaped, max ~60cm — IMPOSSIBLE for cargo bikes |
| **Bollard** | `barrier=bollard` | Variable gap, often <1m — needs individual check |
| **Stile** | `barrier=stile` | Step-over barrier — cargo bike IMPOSSIBLE |
| **Narrow Path** | `width<1.2` | Explicitly tagged narrow paths — route avoidance |

The 4 header filters (Breite, Verkehr, Untergrund, Ladesäulen) provide instant toggle control over these cargo-critical routing parameters without opening any menu or settings panel.

---

## In 30 Sekunden loslegen

> **1.** App öffnen
>
> **2.** Sitzung erstellen oder per Code beitreten
>
> **3.** Start und Ziel eingeben — Route wird berechnet
>
> **4.** Teilnehmer einladen — gemeinsam planen

---

## Features

### Karte
| Feature | Beschreibung |
|---------|-------------|
| **5 Kartenstile** | Street, Dark, Satellite, Topo, Terrain3D — powered by MapLibre GL |
| **3D Terrain** | Echtes 3D-Gelände mit DEM-Tiles, neigbar/drehbar (1.5x Exaggeration) |
| **Hillshade-Overlay** | Geländeschattierung auf jedem Kartenstil |
| **Sonnenaufgangs-Kompass** | SVG-Kompass mit Azimut, Uhrzeiten, NOAA-Formel |
| **Notizen auf der Karte** | Rechtsklick — bearbeitbare/löschbare Notiz-Marker, 6 Farben |
| **Layer-Kontrolle** | Routenlinie, Alt-Routen, POI-Marker, Wegpunkte ein/aus |
| **Kontextmenü** | Rechtsklick auf der Karte für Start/Via/Ziel/Notiz-Platzierung |

### Lastenrad Power-Leiste (Header)
| Filter | Beschreibung |
|--------|-------------|
| **Breite** | Wege mit Barrieren <100cm und Umlaufsperren vermeiden. Sicherheitsprofil aktiv. |
| **Verkehr** | Hauptstraßen, Schnellstraßen und Autobahnen ausgeschlossen. Radfernwege bevorzugt. |
| **Untergrund** | Nur Asphalt und befestigte Oberflächen. Schotter/Sand gemieden (kritisch für 200kg). |
| **Ladesäulen** | Nur Typ 2 / AC mit mindestens 1m Zugang. DC-Schnelllader (CCS/CHAdeMO) gefiltert. |
| **Quick-Action: Ladesäulen** | Ein-Klick-Suche: Typ 2 AC im 15km-Umkreis, sortiert nach Distanz, fly-to nearest. |

### Routenplanung
| Feature | Beschreibung |
|---------|-------------|
| **8 Routing-Profile** | Cargo, FastBike, Trekking, MTB, Safety, Car-Fast, Car-Eco, Walk (BRouter API) |
| **3 Alternativen** | Kürzeste, Schnellste, Sicherste — automatisch berechnet |
| **Via-Stopps** | Unbegrenzte Zwischenstopps per Rechtsklick oder POI |
| **Höhendaten** | Auf-/Abstieg aus BRouter mit Elevation-Fallback |
| **Cargo-Sicherheit** | Lastenrad-Steigungsanalyse: moderate/difficult/extreme Warnungen |
| **Drag & Drop** | Wegpunkte auf der Karte verschieben |

### POI-Entdeckung
| Feature | Beschreibung |
|---------|-------------|
| **19 Kategorien** | Ladesäule, Restaurant, Café, Krankenhaus, Apotheke, Camping, Sehenswürdigkeit, Tankstelle, Trinkwasser, Fahrradwerkstatt, Parking, Hotel, ATM, Bäckerei + Cargo-Sicherheit (Barriere, Engpass, Oberfläche) |
| **SVG-Icon-System** | Professionelle minimalistische Inline-SVG-Icons für alle Kategorien (keine Emojis) |
| **Bis 100km Radius** | 5km, 10km, 25km, 50km, 100km — erweiterter Suchradius |
| **Klickbare Filter** | Farbcodierte Kategorie-Chips zum Ein-/Ausschalten |
| **Overpass API** | FIFO Queue + Endpoint-Racing + 10min Cache + 3-Endpoint Racing |
| **Typ 2 / AC Query** | Dedizierte Overpass-Query: `socket:type2=yes`, 15km max, DC-Filter |
| **Ladesäulen-Details** | Anschlüsse, Steckertypen, Leistung, Bezahlung, Website |
| **Smart Dedup** | Union-Find Algorithmus mergt OSM + lokale Daten (50m-200m Radius) |
| **500km+ Support** | Automatische 50km-Sektoren-Segmentierung |
| **1m Lastenrad-Check** | POI-Zugänglichkeit: bicycle, access, maxwidth Tags |
| **8 lokale EV-Datensätze** | 97 Offline-Backup Stationen in 8 Ländern (HR, TR, RS, BA, ME, MK, BG, GR) |

### Höhenprofil
| Feature | Beschreibung |
|---------|-------------|
| **Interaktives SVG** | Overlay oben an der Karte, aufklappbar |
| **Steigungsfarben** | Grün (flach), Gelb (leicht), Orange (mittel), Rot (steil) — Cargo-spezifisch (4/6/10%) |
| **Hover-Tooltip** | km-Position, Höhe, Steigung in % + Marker auf der Karte |
| **>10% Warnung** | Rote Markierung bei kritischen Steigungen für 200kg Gesamtgewicht |

### Kollaborative Sitzung
| Feature | Beschreibung |
|---------|-------------|
| **Session-Code** | 6-stelliger kryptographisch sicherer Code (crypto.getRandomValues) |
| **3 Rollen** | Host (volle Rechte), Editor (Wegpunkte/Routen ändern), Viewer (nur lesen) |
| **Anti-Echo** | Remote-Updates werden nicht zurückgesendet |
| **Web-Chat** | Echtzeit-Chat für alle Teilnehmer der Sitzung |
| **Farb-System** | Jeder Teilnehmer bekommt eine eindeutige Farbe |

### Export
| Feature | Beschreibung |
|---------|-------------|
| **GPX / KML / GeoJSON** | Standard-Formate mit Wegpunkten und Track |
| **Google Maps / Apple Maps / Waze / HERE / Organic Maps** | Direkte Navigationslinks |
| **JSON Import/Export** | 1:1 Route mit allen Daten (Import-fähig) |

### Professional UI
| Feature | Beschreibung |
|---------|-------------|
| **SVG-Icon-System** | Alle Icons als minimalistische Inline-SVGs (keine Emojis, keine externen Dependencies) |
| **Tooltip auf jedem Element** | Hover-Tooltips mit präziser technischer Erklärung |
| **Kollabierbare Options** | Settings-Panel für Sitzung, Chat, Export, Karte |
| **Responsive** | Desktop Dashboard + Mobile Listener-Mode |

---

## Tar-Struktur (Release-Paket)

```
RouteSync-vX.Y.Z.tar              <- Release-Tar (alles in einem)
|-- RouteSync-vX.Y.Z-source.tar   <- Kompletter Source + README + CHANGELOG + VERSION
|-- RouteSync-vX.Y.Z-static.tar   <- Production Build (dist/) + README + CHANGELOG + VERSION
`-- VERSION
```

---

## Projektstruktur

```
src/
|-- app/
|   |-- globals.css              # Tailwind CSS v4 + Theme + Glass
|   |-- layout.tsx               # Root Layout (ThemeProvider)
|   `-- page.tsx                 # Main App (Desktop/Mobile)
|-- types/
|   `-- index.ts                 # TypeScript Interfaces & POI Config
|-- store/
|   |-- useRouteStore.ts         # Wegpunkte & Routendaten
|   |-- useSessionStore.ts       # Sitzung, Chat, Teilnehmer, Sync-Bridge
|   |-- usePOIStore.ts           # POI-Suche & Filter
|   |-- useMapStore.ts           # Karten-Einstellungen + Lastenrad-Filter
|   `-- useElevationStore.ts     # Höhenprofil-Daten & Cache
|-- lib/
|   |-- overpass.ts              # Overpass API (Typ 2 Query, Racing, Cache, Queue)
|   |-- poi-aggregator.ts        # Multi-Source POI Aggregator (Tile-Cache, Sektoren)
|   |-- poi-tiles.ts             # 1x1 Tile-Cache (IndexedDB, 7d TTL)
|   |-- routing.ts               # BRouter API (8 Profile, Cargo-Grade-Limits)
|   |-- elevation.ts             # Open-Meteo Elevation (Batch, Queue)
|   |-- export.ts                # GPX, KML, GeoJSON, Maps-Links, JSON
|   |-- geocode.ts               # Nominatim Geocoding + User-Agent
|   `-- peerSync.ts              # PeerJS WebRTC Sync-Bridge
|-- components/
|   |-- icons/index.tsx          # SVG Icon System (20+ minimalist Icons)
|   |-- map/
|   |   |-- MapView.tsx          # MapLibre GL Karte + POI/Note-Marker + Terrain
|   |   |-- MapControls.tsx      # Zoom, Style, Layer-Toggles, 3D (SVG Icons)
|   |   |-- ElevationProfile.tsx # Interaktives Höhenprofil SVG
|   |   `-- SunriseCompass.tsx   # Sonnenaufgangs-Kompass Overlay
|   |-- dashboard/
|   |   |-- DashboardLayout.tsx  # Drei-Zonen-Layout
|   |   |-- HeaderBar.tsx        # Power-Leiste + Typ 2 Quick-Action + Filter
|   |   |-- LeftSidebar.tsx      # Route-Konfiguration
|   |   |-- RightSidebar.tsx     # POI + Dynamic Summary
|   |   `-- SettingsPanel.tsx    # Settings Slide-Over
|   |-- sidebar/
|   |   |-- Sidebar.tsx          # 5-Tab Layout (Fallback)
|   |   |-- RoutePanel.tsx       # Routenplanung
|   |   |-- POIPanel.tsx         # POI-Suche
|   |   |-- SessionPanel.tsx     # Teilnehmer-Verwaltung
|   |   |-- ChatPanel.tsx        # Web-Chat
|   |   `-- ExportPanel.tsx      # Export-Optionen
|   `-- ui/                      # shadcn/ui Komponenten
```

---

## Entwicklung

```bash
bun install
bun run dev
bun run build
bun run release
```

---

## Tech Stack

| Technologie | Zweck |
|-----------|-------|
| ![Next.js](https://img.shields.io/badge/Next.js-000?logo=next.js) | Framework (v16, App Router) |
| ![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript) | Type Safety (v5.8) |
| ![Tailwind CSS](https://img.shields.io/badge/Tailwind-06B6D4?logo=tailwindcss) | Styling (v4) |
| ![Zustand](https://img.shields.io/badge/Zustand-764ABC) | State Management (v5) |
| ![MapLibre GL](https://img.shields.io/badge/MapLibre-1AAC71?logo=maplibre) | Karten-Rendering (v5.22) |

---

## APIs

| API | Zweck | Kosten |
|-----|-------|--------|
| [BRouter](https://brouter.de) | Routenberechnung (8 Profile) | Kostenlos |
| [Nominatim](https://nominatim.org) | Geocoding / Adresssuche | Kostenlos |
| [Overpass](https://overpass-api.de) | POI-Suche + Typ 2 Ladesäulen | Kostenlos |
| [CARTO Tiles](https://carto.com) | Kartenstile (Street/Dark) | Kostenlos |
| [OpenTopoMap](https://opentopomap.org) | Topographische Karte | Kostenlos |
| [Esri](https://esri.com) | Satellitenbild-Karte | Kostenlos |

---

## Versionshistorie

Alle Änderungen sind detailliert in der [CHANGELOG.md](./CHANGELOG.md) dokumentiert.

| Version | Datum | Typ | Beschreibung |
|---------|-------|-----|-------------|
| **1.5.6** | 2026-04-17 | Patch | Rechte Sidebar Clipping Fix: Radix ScrollArea durch nativen Scroll ersetzt |
| **1.5.5** | 2026-04-17 | Patch | POI-Datenquelle auf RouteSync-POI-Data korrigiert (fresh repo, 3019 Tiles, 186K POIs) |
| **1.5.4** | 2026-04-17 | Minor | Worldwide POI-Datenbank: 186K POIs, 3.019 Tiles, 80+ Länder |
| **1.5.3** | 2026-04-16 | Patch | Release-Tar Struktur korrigiert: source.tar + static.tar + VERSION |
| **1.5.2** | 2026-04-16 | Patch | Dokumentation-Refresh: Alle Versionen auf 1.5.2, SDD aktualisiert |
| **1.5.1** | 2026-04-16 | Patch | Release-Tar Struktur-Fix: Source/Static-Tar + VERSION |
| **1.5.0** | 2026-04-16 | Minor | System-Audit, POI-Popup, Phase 0.5 Caching, 6 Bugfixes |
| **1.4.1** | 2026-04-16 | Patch | POI-Suche Crash: uncachedTileIds Scope-Error |
| **1.4.0** | 2026-04-16 | Minor | GitHub POI-Datenbank, Tile-Generator, POI-Info-Verbesserung |
| **1.3.5** | 2026-04-16 | Patch | Cluster-Click Crash Fix, NaN-Guard, style.load Dedup |
| **1.3.4** | 2026-04-15 | Patch | Sidebar Clipping Fix, POI Rendering Robustheit |
| **1.3.3** | 2026-04-15 | Patch | POI sourcedata Endlosschleife entfernt |
| **1.3.2** | 2026-04-15 | Patch | Elevation-Profile Fix, POI Layer-Self-Healing |
| **1.3.1** | 2026-04-15 | Patch | POI Rendering Pipeline: StyleLoadCount, Self-Healing, Filter |
| **1.3.0** | 2026-04-15 | Major | POI Rendering Fix: Layer-Data Separation, High-Zoom Cluster-Expand |
| **1.2.2** | 2026-04-15 | Patch | Dreistufige POI-Suchpriorität: Standort > Route > Center |
| **1.2.1** | 2026-04-15 | Patch | MapLibre CSS-Var Paint-Property Fix (POI-Labels) |
| **1.1.0** | 2026-04-15 | Minor | Typ 2 AC Overpass Query, 1m Breite Verification Docs, README Rewrite |
| **1.0.0** | 2026-04-15 | Major | Lastenrad Cockpit: Power-Leiste, Anti-Redundanz, Tooltips, Expert Features |
| **0.9.0** | 2026-04-15 | Major | Dashboard-Layout: Header + Links/Rechts Sidebar, Settings-Panel |
| **0.8.1** | 2026-04-15 | Patch | Center-Query Tile-Cache, Center-Radius-Filter, Brand-Regex |
| **0.8.0** | 2026-04-15 | Major | Tile-basierter POI-Cache (IndexedDB, 7d TTL) |
| **0.7.2** | 2026-04-14 | Minor | Höhenprofil 500km Fix, Ladesäulen-Expansion (TR/Balkan) |
| **0.7.0** | 2026-04-14 | Major | 500km Sektoren-Segmentierung, Endpoint-Racing, Lastenrad-Check |
| **0.6.0** | 2026-04-14 | Major | Cargo Bike Routing: Barriere-Check, Oberflächen, Steigungs-Sicherheit |

---

<div align="center">

**Built for cargo bike world travel**

</div>
