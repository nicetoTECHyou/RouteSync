<div align="center">

# RouteSync

### Kollaborative Routenplanung für Jedermann

**Gemeinsam Routen planen — POIs entdecken — 1:1 Export**

**RouteSync v0.7.1**

<br>

[![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=next.js&logoColor=white)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.8-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![MapLibre](https://img.shields.io/badge/MapLibre_GL-5.22-1AAC71?logo=maplibre&logoColor=white)](https://maplibre.org)
[![Zustand](https://img.shields.io/badge/Zustand-5-764ABC)](https://github.com/pmndrs/zustand)
[![Version](https://img.shields.io/badge/Version-0.7.1-10B981)](./CHANGELOG.md)

<br>

<img src="https://img.shields.io/badge/Installieren-0%20€-success?style=flat-square" /> &nbsp;
<img src="https://img.shields.io/badge/API_Key_nötig-NEIN-success?style=flat-square" /> &nbsp;
<img src="https://img.shields.io/badge/100%25_Open_Source-✓-10B981?style=flat-square" />

</div>

---

## In 30 Sekunden loslegen

RouteSync ist eine Web-App — nichts installieren, nichts registrieren.

> **1.** App öffnen
>
> **2.** Sitzung erstellen oder per Code beitreten
>
> **3.** Start und Ziel eingeben — Route wird berechnet
>
> **4.** Teilnehmer einladen — gemeinsam planen

---

## Ubersicht

RouteSync verwandelt die Routenplanung in ein **kollaboratives Erlebnis**. Erstelle eine Sitzung, teile den Code und plane Routen gemeinsam mit Freunden, Reisebegleitern oder deinem Team. POIs werden direkt auf der Karte angezeigt und per Klick in die Route aufgenommen. Fertige Routen exportierst du als GPX, Google Maps Link oder JSON.

---

## Features

### Karte
| Feature | Beschreibung |
|---------|-------------|
| **5 Kartenstile** | Street, Dark, Satellite, Topo, Terrain3D — powered by MapLibre GL |
| **3D Terrain** | Echtes 3D-Gelände mit DEM-Tiles, neigbar/drehbar (1.5x Exaggeration) |
| **Hillshade-Overlay** | Geländeschattierung auf jedem Kartenstil |
| **Sonnenaufgangs-Kompass** | SVG-Kompass mit Azimut, Uhrzeiten, NOAA-Formel, updated bei Pan/Zoom |
| **Notizen auf der Karte** | Rechtsklick → bearbeitbare/löschbare Notiz-Marker, 6 Farben |
| **Layer-Kontrolle** | Routenlinie, Alt-Routen, POI-Marker, Wegpunkte, Straßennamen ein/aus |
| **Kontextmenü** | Rechtsklick auf der Karte für Start/Via/Ziel/Notiz-Platzierung |

### Routenplanung
| Feature | Beschreibung |
|---------|-------------|
| **8 Routing-Profile** | Cargo, FastBike, Trekking, MTB, Safety, Car-Fast, Car-Eco, Walk (BRouter API) |
| **3 Alternativen** | Kürzeste, Schnellste, Sicherste — automatisch berechnet |
| **Via-Stopps** | Unbegrenzte Zwischenstopps per Rechtsklick oder POI |
| **Höhendaten** | Auf-/Abstieg aus BRouter mit Elevation-Fallback |
| **Post-Processing** | Routen-Kategorien werden anhand tatsächlicher Metriken validiert |
| **Drag & Drop** | Wegpunkte auf der Karte verschieben |

### POI-Entdeckung
| Feature | Beschreibung |
|---------|-------------|
| **19 Kategorien** | Ladesäule, Restaurant, Café, Krankenhaus, Apotheke, Camping, Sehenswürdigkeit, Tankstelle, Trinkwasser, Fahrradwerkstatt, Parking, Hotel, ATM, Bäckerei + Cargo-Sicherheit (Barriere, Engpass, Oberfläche) |
| **Bis 100km Radius** | 5km, 10km, 25km, 50km, 100km — erweiterter Suchradius |
| **Emoji-Icons** | Professionelle Emoji-Icons für alle 16 Kategorien |
| **Klickbare Filter** | Farbcodierte Kategorie-Chips zum Ein-/Ausschalten |
| **Overpass API** | Echte OpenStreetMap-Daten mit FIFO Queue + Endpoint-Racing + 10min Cache |
| **Ladesäulen-Details** | Anschlüsse, Steckertypen, Leistung, Bezahlung, Website |
| **Smart Dedup** | Union-Find Algorithmus mergt OSM + lokale Daten (50m-200m Radius) |
| **500km+ Support** | Automatische 50km-Sektoren-Segmentierung für Langstrecken (unsichtbar für User) |
| **Lastenrad-Check** | POI-Zugänglichkeit für 1m+ breite Lastenräder (bicycle/access/maxwidth) |
| **8 lokale EV-Datensätze** | 97 Offline-Backup Stationen in 8 Ländern (HR, TR, RS, BA, ME, MK, BG, GR) |
| **Add to Route** | Jeder POI hat "Via" und "Ziel" Buttons direkt im Popup |

### Höhenprofil
| Feature | Beschreibung |
|---------|-------------|
| **Interaktives SVG** | Overlay oben an der Karte, aufklappbar |
| **Steigungsfarben** | Grün (flach), Gelb (leicht), Orange (mittel), Rot (steil) — Cargo-spezifisch (4/6/10%) |
| **Hover-Tooltip** | km-Position, Höhe, Steigung in % + Marker auf der Karte |
| **Cargo-Sicherheit** | Lastenrad-Steigungsanalyse: moderate/difficult/extreme Warnungen |
| **Open-Meteo API** | Kostenloses Elevation-API, max 2000 Punkte, Batching mit Cache + 429-Schutz |

### Kollaborative Sitzung
| Feature | Beschreibung |
|---------|-------------|
| **Session-Code** | 6-stelliger kryptographisch sicherer Code (crypto.getRandomValues) |
| **3 Rollen** | Host (volle Rechte), Editor (Wegpunkte/Routen ändern), Viewer (nur lesen) |
| **Schreibrechte** | Host kann Teilnehmern Editor-Rechte geben oder entziehen |
| **Kick-User** | Host kann Teilnehmer aus der Session entfernen |
| **Anti-Echo** | Remote-Updates werden nicht zurückgesendet (Echo-Vermeidung) |
| **Web-Chat** | Echtzeit-Chat für alle Teilnehmer der Sitzung |
| **Farb-System** | Jeder Teilnehmer bekommt eine eindeutige Farbe |

### Export
| Feature | Beschreibung |
|---------|-------------|
| **GPX** | Kompatible GPX-Datei mit Wegpunkten und Track (XML-Injection geschützt) |
| **KML** | Google Earth kompatibles KML-Format |
| **GeoJSON** | Standard-GeoJSON für weitere Verarbeitung |
| **Google Maps** | Direkter Link mit allen Wegpunkten + Fahrrad-Modus |
| **Apple Maps** | Multi-Stop Route mit Via-Punkten |
| **Waze** | Direkter Navigations-Link |
| **HERE WeGo** | HERE Maps Navigation |
| **Organic Maps** | Alle Wegpunkte als Route übergeben |
| **JSON** | 1:1 Route mit allen Daten (Import-fähig) |
| **Import** | JSON-Import mit Validierung und automatischer Wiederherstellung |

### Mobile
| Feature | Beschreibung |
|---------|-------------|
| **Listener Mode** | Mobile Nutzer (< 768px) sehen Vollbild-Karte + Chat |
| **Touch-optimiert** | POI-Popups, Kartensteuerung, Chat-Eingabe |
| **Responsive** | Sidebar wird auf Mobilgeräten automatisch ausgeblendet |

---

## Tar-Struktur (Release-Paket)

```
RouteSync-vX.Y.Z.tar              ← Release-Tar (alles in einem)
├── RouteSync-vX.Y.Z-source.tar   ← Kompletter Source + README + CHANGELOG + VERSION
├── RouteSync-vX.Y.Z-static.tar   ← Production Build (dist/) + README + CHANGELOG + VERSION
└── VERSION
```

**Inhalt des Source-Tars:**
```
RouteSync-vX.Y.Z/
├── src/                     ← Kompletter TypeScript-Source
├── public/                  ← Statische Assets
├── prisma/                  ← Datenbank-Schema
├── scripts/                 ← Build- und Release-Skripte
├── package.json             ← Dependencies & Scripts
├── tsconfig.json            ← TypeScript-Konfiguration
├── next.config.ts           ← Next.js-Konfiguration
├── postcss.config.mjs       ← PostCSS-Konfiguration
├── eslint.config.mjs        ← ESLint-Konfiguration
├── tailwind.config.ts       ← Tailwind-Konfiguration
├── components.json          ← shadcn/ui Konfiguration
├── README.md                ← Diese Datei
├── CHANGELOG.md             ← Versionshistorie
├── VERSION                  ← Aktuelle Version
├── Caddyfile                ← Caddy Reverse-Proxy Konfiguration
└── bun.lock                 ← Dependency-Lockfile
```

**Inhalt des Static-Tars:**
```
RouteSync-vX.Y.Z/
├── dist/                    ← Production Build (Next.js Static Export)
├── README.md                ← Diese Datei
├── CHANGELOG.md             ← Versionshistorie
└── VERSION                  ← Aktuelle Version
```

| Typ | Format | Beschreibung |
|-----|--------|-------------|
| **Patch** | x.x.Z | Bug Fixes, kleine Korrekturen |
| **Minor** | x.Y.0 | Große Fixes, Security, Route-Änderungen |
| **Major** | X.0.0 | Neue Features, Breaking Changes |

> **WICHTIG — Static-Tar Inhalts-Regel:** Nach jedem Build MUSS `dist/` die Dateien `README.md`, `CHANGELOG.md` und `VERSION` enthalten.

---

## Projektstruktur

```
src/
├── app/
│   ├── globals.css              # Tailwind CSS v4 + Theme + Glass
│   ├── layout.tsx               # Root Layout (ThemeProvider)
│   └── page.tsx                 # Main App (Desktop/Mobile)
├── types/
│   └── index.ts                 # TypeScript Interfaces & POI Config
├── store/
│   ├── useRouteStore.ts         # Wegpunkte & Routendaten
│   ├── useSessionStore.ts       # Sitzung, Chat, Teilnehmer, Sync-Bridge
│   ├── usePOIStore.ts           # POI-Suche & Filter
│   ├── useMapStore.ts           # Karten-Einstellungen (3D, Layer)
│   └── useElevationStore.ts     # Höhenprofil-Daten & Cache
├── lib/
│   ├── utils.ts                 # cn(), formatDistance/Duration
│   ├── session.ts               # Session-Code (crypto-safe), Teilnehmer-Verwaltung
│   ├── peerSync.ts              # PeerJS WebRTC Sync-Bridge
│   ├── geocode.ts               # Nominatim Geocoding + User-Agent
│   ├── routing.ts               # BRouter API (7 Profile, 3 Kategorien, Dedup)
│   ├── overpass.ts              # Overpass API (Retry/Fallback, Duplicate-Guard)
│   ├── elevation.ts             # Open-Meteo Elevation (Batch, Retry, Backoff)
│   ├── sun.ts                   # NOAA Sonnenstands-Berechnung
│   ├── export.ts                # GPX, KML, GeoJSON, Maps-Links, JSON (XML-safe)
│   ├── poiFormatter.ts          # POI Popup-Formatter (Ladesäulen, etc.)
│   └── db.ts                    # Prisma ORM (Query-Logging Dev-only)
├── components/
│   ├── map/
│   │   ├── MapView.tsx          # MapLibre GL Karte + POI/Note-Marker + Terrain
│   │   ├── MapControls.tsx      # Zoom, Style, Layer-Toggles, 3D-Controls
│   │   ├── ElevationProfile.tsx # Interaktives Höhenprofil SVG
│   │   └── SunriseCompass.tsx   # Sonnenaufgangs-Kompass Overlay
│   ├── sidebar/
│   │   ├── Sidebar.tsx          # 5-Tab Layout
│   │   ├── RoutePanel.tsx       # Routenplanung
│   │   ├── POIPanel.tsx         # POI-Suche
│   │   ├── SessionPanel.tsx     # Teilnehmer-Verwaltung
│   │   ├── ChatPanel.tsx        # Web-Chat
│   │   └── ExportPanel.tsx      # Export-Optionen
│   ├── session/
│   │   ├── JoinOverlay.tsx      # Landing/Create/Join
│   │   └── SessionHeader.tsx    # Session-Info-Bar
│   └── ui/                      # shadcn/ui Komponenten
```

---

## Entwicklung

```bash
# Abhängigkeiten installieren
bun install

# Dev-Server starten (localhost:3000)
bun run dev

# Production Build erstellen
bun run build

# Build starten
bun run start

# Lint prüfen
bun run lint

# Source-Tar erstellen
bun run package:source

# Build + Source-Tar
bun run package:release
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
| ![Radix UI](https://img.shields.io/badge/Radix_UI-6E56CF?logo=radixui) | UI-Komponenten (shadcn/ui) |

---

## APIs

| API | Zweck | Kosten |
|-----|-------|--------|
| [BRouter](https://brouter.de) | Routenberechnung (7 Profile) | Kostenlos |
| [Nominatim](https://nominatim.org) | Geocoding / Adresssuche | Kostenlos |
| [Overpass](https://overpass-api.de) | POI-Suche (16 Kategorien) | Kostenlos |
| [CARTO Tiles](https://carto.com) | Kartenstile (Street/Dark) | Kostenlos |
| [OpenTopoMap](https://opentopomap.org) | Topographische Karte | Kostenlos |
| [Esri](https://esri.com) | Satellitenbild-Karte | Kostenlos |

---

## Versionshistorie

Alle Änderungen sind detailliert in der [CHANGELOG.md](./CHANGELOG.md) dokumentiert.

| Version | Datum | Typ | Beschreibung |
|---------|-------|-----|-------------|
| **0.7.1** | 2026-04-14 | Patch | Korridor-Filter, Brand-Integration in Sektoren, overpass.ru entfernt, Category-Fix |
| **0.7.0** | 2026-04-14 | Major | 500km Sektoren-Segmentierung, Endpoint-Racing, Result-Cache, Lastenrad-Check |
| **0.6.3** | 2026-04-14 | Patch | Overpass Queue v3: 60s Timeout, Endpoint Health, Inter-Query Delay |
| **0.6.1** | 2026-04-14 | Patch | Overpass FIFO Queue + 429 Rate-Limit Fix (24-Req-Burst eliminiert) |
| **0.6.0** | 2026-04-14 | Major | Cargo Bike (Lastenrad) Routing: Barriere-Check, Oberflächen, Steigungs-Sicherheit, Griechenland |
| **0.5.9** | 2026-04-14 | Critical | POI BBox Queries, Turkish Station Expansion, Case-Insensitive Brand-Suche |
| **0.5.8** | 2026-04-14 | Critical | Elevation 429 Queue-Fix, Overpass Parallel Failover, Deep Audit |
| **0.5.1** | 2026-04-14 | Minor | Code-Dedup, Parallel Queries, Graceful Degradation |
| **0.5.0** | 2026-04-14 | Major | POI Aggregator: Multi-Source, Smart Dedup, 50 Local EV Stations (4 Länder) |
| **0.4.4** | 2026-04-14 | Patch | POI Detail-Expand, Sidebar Overflow Safety |
| **0.4.3** | 2026-04-14 | Patch | Static-Tar enthält jetzt README.md + CHANGELOG.md, Release-Script gefixt |
| **0.4.2** | 2026-04-14 | Minor | Code-Review: 3 Critical, 10 High Fixes (Routing, Elevation, Sync, Export) |
| **0.4.1** | 2026-04-14 | Patch | MapView TDZ-Crash Fix — Production White-Screen behoben |
| **0.4.0** | 2026-04-14 | Minor | Security: Window-Globals eliminiert, XML-Injection-Schutz, Sync-Bugfixes |
| **0.3.7** | 2026-04-13 | Patch | API Rate-Limit Fix, Elevation Retry-Loop, Nominatim User-Agent, DEM-Source Spaltung |
| **0.3.6** | 2026-04-13 | Patch | ElevationProfile Marker Crash Fix — maplibre-gl CJS/ESM Interop (Production) |
| **0.3.5** | 2026-04-13 | Patch | ElevationProfile Marker Crash Fix — dynamischer Import mit try-catch |
| **0.3.0** | 2026-04-13 | Major | Höhenprofil — interaktives SVG-Diagramm mit Steigungsfarben |
| **0.2.8** | 2026-04-13 | Minor | Sonnenaufgangs-Kompass, Notizen auf Karte, Navigationsexport überarbeitet |
| **0.2.7** | 2026-04-13 | Minor | Ladesäulen-Details erweitert (Anschlüsse, Leistung, Bezahlung) |
| **0.2.6** | 2026-04-13 | Major | 3D Terrain, Multi-Navigationsexport, POI 100km, Emoji-Icons |
| **0.1.2** | 2026-04-13 | Patch | Hotfix — React Hydration Error #418 |
| **0.1.1** | 2026-04-13 | Patch | Hotfix — GitHub Pages Asset 404 |
| **0.1.0** | 2026-04-13 | Major | Erstveröffentlichung — Karten, Routing, POI, Session, Chat, Export, Mobile |

---

<div align="center">

**Built for collaborative route planning**

</div>
