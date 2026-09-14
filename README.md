# 🚚 RoutePro KSA - Truck Route & Fuel Calculator

> Fleet road routing & fuel system for KSA heavy-truck operations — **zero-build, single-file web app**.

A pure **client-side** application for planning **multi-stop truck routes**, simulating the **fuel & mission math** (distance, liters, tank sufficiency, driving time, mandatory rest breaks, road delays, ETA) and pushing results into **KML (Google Earth)**, a **Google Maps directions link**, or a **WhatsApp summary** — everything inside one HTML file.

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-38B2AC?style=for-the-badge&logo=tailwindcss&logoColor=white)
![Leaflet](https://img.shields.io/badge/Leaflet%201.9-199900?style=for-the-badge&logo=leaflet&logoColor=white)
![OpenStreetMap](https://img.shields.io/badge/OpenStreetMap-7EBC6F?style=for-the-badge&logo=openstreetmap&logoColor=white)
![Geoapify Routing](https://img.shields.io/badge/Geoapify%20Routing-1e293b?style=for-the-badge)
![Font Awesome](https://img.shields.io/badge/Font%20Awesome-528DD7?style=for-the-badge&logo=fontawesome&logoColor=white)
![100% Client-Side](https://img.shields.io/badge/100%25%20Client--Side-0ea5e9?style=for-the-badge)
![No Build Required](https://img.shields.io/badge/No%20Build%20Required-16a34a?style=for-the-badge)
![Status: Active](https://img.shields.io/badge/Status-Active-22c55e?style=for-the-badge)

---

## ✨ Features

| 🎯 Feature | What it does |
|---|---|
| 🗺️ **Multi-stop route planning** | Start point, **drag-and-drop reorderable** destination stops, optional return point |
| 🖱️ **Click-to-set on map** | Click anywhere and set it as **Start** / **Add as Stop** / **Return** from one popup |
| 📍 **Live coordinate preview** | Markers + permanent tooltips update as you type `lat, lon` and auto-fit the view |
| 🔍 **Jump-to-coordinates widget** | Paste `lat, lon` and fly the map to that spot instantly |
| 🧭 **5 base-map styles** | Standard OSM, English Streets (Esri), Voyager (CARTO), Light Map (Carto), Hybrid Satellite (Esri) |
| ⚖️ **Truck load presets** | Light Truck (3.5t) / Medium Truck (15t) / Heavy Truck Partial (28t) / Heavy Truck Full (42t) / Extra Heavy (45t) — configurable in Settings; each auto-applies a realistic km/L efficiency |
| 🛻 **Vehicle Profile confirmation** | Choose a Geoapify routing profile + confirm vehicle dimensions (weight/height/width/length) per calculation |
| ⛽ **Fuel tank module** | Tank fuel, **auxiliary fuel** (PTO / idling / reefer), km/L, price (SAR/L) — **surplus** or **refuel-deficit** status |
| 🛡️ **Saudi TGA Compliance Mode** | Enforces configurable max driving, mandatory breaks, and daily rest rules for KSA driver hours-of-service |
| ⏱️ **Mission timeline & ETA** | Departure date/time → arrival, split into driving / breaks / daily rest / delays |
| 🛣️ **Geoapify truck routing** | Real road polyline & distance; **Haversine straight-line fallback** with dashed line if offline |
| 📦 **Exports & integrations** | **KML** (Google Earth pins + path), **Google Maps link** (with waypoints), **WhatsApp** summary |
| 💾 **Profile save / load** | Full parameter set exported / imported as **JSON** profile files |
| 🗄️ **SQLite database** | Routes saved locally in browser via sql.js + IndexedDB persistence |
| ⚙️ **Settings panel** | Live preview + show/hide KML, GMaps, WhatsApp toolbar buttons — saved in `localStorage` |

---

## 🚀 Quick Start

No build step — just open the file:

1. Open **`index.html`** in any modern browser.
   - Double-clicking works for basic use, but some map tiles and CDN resources may be blocked by browser `file://` security policies.
   - For the full experience, serve it via a local server, e.g.:
     - VS Code Live Server extension
     - `python -m http.server 8080` then open `http://localhost:8080`
2. Enter your **Geoapify API key** in **Settings** (the app uses a user-provided key, not a hardcoded one).
3. Enter a start coordinate in `lat, lon` format, e.g. Riyadh: `24.7136, 46.6753`.
    - Easier: toggle **Click to Set: ON**, click the map — or use the **Jump to Coordinates** widget.
4. Add destination stops with **+ Add Stop** (drag 🖐️ to re-order) and set an optional return point (leave empty for one-way).
5. Choose truck **load weight**, set **available fuel**, **extra fuel**, **km/L**, **price/L** (SAR), **max speed**, **delays** and **departure** date/time.
6. Keep **Saudi TGA Compliance** ON (default) for KSA driver rules — or switch OFF for custom rest intervals.
7. Press **🧮 Calculate Truck Route & Fuel** and read the summary cards + modal.

> ⚠️ Internet is required — tile servers, Geoapify routing API and CDNs load at runtime. If the router is unavailable, the app falls back to straight-line (approximate) distances with a dashed route preview.

---

## 🔑 Geoapify API Key

This app uses a **user-based API key model**:

- Each user enters their own **Geoapify API key** in **Settings**.
- The key is stored only in the user’s browser `localStorage`.
- The key is never sent to any server except **Geoapify** itself during routing requests.

**Security advice for users:**

- In your [Geoapify account](https://my.geoapify.com/), restrict your API key to:
  - **Allowed Origins**: your deployment domain (e.g. `https://your-app.vercel.app`)
  - **Allowed HTTP referrers**: `https://*.vercel.app/*` (for Vercel deployments)
  - **Allowed IP addresses**: leave empty (Vercel uses dynamic edge IPs)
  - **CORS Access-Control-Allow-Origin**: set to your exact origin, not `*`
- Monitor your Geoapify usage dashboard regularly.
- Never share your API key publicly.

---

## 🧮 Under the Hood (calculation engine)

```text
Road distance (km)   = Geoapify driving route      (fallback: Haversine straight line)
Driving fuel (L)     = distance ÷ efficiency (km/L)
Extra fuel (L)       = auxiliary / PTO / idling / reefer fuel
Fuel required (L)    = driving fuel + extra
Fuel cost (SAR)      = fuel required × price (SAR/L)
Driving time (h)     = distance ÷ max speed (km/h)
Rest time (h)        = TGA rules  OR  custom interval × rest hours
Total mission (h)    = driving + rests + daily rest + road delays
ETA                  = departure date/time + total mission time
```

### ⚖️ Truck load → efficiency presets

| Load status | Weight | Built-in efficiency |
|---|---|---|
| 🟢 Light Truck (Dyna) | 3.5 t | 3.5 km/L |
| 🟡 Medium Truck (Rigid 6x4) | 15 t | 2.5 km/L |
| 🟠 Heavy Truck (Partial Load 2-3 Axle Trailer) | 28 t | 1.9 km/L |
| 🔴 Heavy Truck (Full Load 4-Axle Semi-Trailer) | 42 t | 1.6 km/L |
| ⚫ Extra Heavy / Articulated (5-Axle Max Legal Limit) | 45 t | 1.4 km/L |

> Load presets are configurable in **Settings**. The **Truck Load Weight** selector affects **fuel efficiency** only.

### 🛻 Vehicle Profile vs Truck Load Weight

These are **two separate concepts**:

- **Truck Load Weight** — affects **fuel efficiency** (km/L) defaults.
- **Vehicle Profile** (in the Confirm Vehicle Profile modal) — affects **Geoapify routing mode** and vehicle **dimensions** (weight, height, width, length) sent to the routing API.

### 🛡️ Saudi TGA Compliance rules (default ON)

| Rule | Default | Configurable |
|---|---|---|
| Max continuous driving per stint | **4.5 h** | Settings |
| Mandatory break after each stint | **45 min** | Settings |
| Daily rest when driving > threshold | **11 h** | Settings |

Breaks count uses the configured driving limit. With TGA OFF the app uses your custom **Rest interval (km)** and **Rest hours per interval**.

---

## 🧩 Inputs & Parameters

| Parameter | Unit | Notes |
|---|---|---|
| Start point / destination stops / return point | `lat, lon` | Click map or paste coordinates |
| Truck load weight | — | Configurable presets in Settings; first preset is default |
| Available fuel in tank | L | Default set in Settings |
| Extra fuel (auxiliary / PTO / idling / reefer) | L | Default set in Settings |
| Fuel efficiency | km/L | Auto-filled from load preset; overridable |
| Fuel price | SAR/L | Default set in Settings |
| Max truck speed | km/h | Default set in Settings |
| Road delay | hrs | Default set in Settings |
| Rest interval | km | Default set in Settings |
| Rest hours per interval | hrs | Default set in Settings |
| Saudi TGA compliance mode | on/off | ON by default; thresholds configurable in Settings |
| Departure date & time | local datetime | Defaults to current date/time |
| Geoapify API key | — | User-provided; stored in browser localStorage only |

---

## 📊 Mission Summary

| Output | Description |
|---|---|
| 📏 **Total Distance** | Route length (km) |
| ⛽ **Fuel Required** | Liters + cost in **SAR** |
| ⏱️ **Travel Duration** | Driving + rests + delays (h/m) |
| 📅 **Expected Arrival** | Local timestamp computed from departure |
| ✅ / ⚠️ **Fuel Status** | Sufficient (surplus L) or refuel needed (deficit L) |
| 🧾 **Mission Summary** | Load label, stop count, road vs approximate route |

---

## 📁 Project Structure

```text
RoutePro KSA/
├── index.html   # The entire app — UI, map, engine, SQLite DB & exports
└── README.md    # This documentation
```

Single-file architecture — HTML structure, Tailwind styling, Leaflet map and all Vanilla JS (`LogisticsOpenStreetMapApp` class) ship in one file with **zero compile step**.

**Data persistence:**
- **Settings & preferences**: `localStorage`
- **Routes database**: SQLite via `sql.js`, persisted to **IndexedDB** so routes survive browser restarts

---

## 🛠️ Built With

| Stack | Purpose | Source |
|---|---|---|
| 🟨 JavaScript (ES6) | All application logic — no frameworks | Inline |
| 🟦 HTML5 + Tailwind CSS | Responsive UI & styling | Tailwind CDN |
| 🍃 Leaflet 1.9 | Interactive map | unpkg CDN |
| 🗺️ OpenStreetMap / Esri / CARTO | Base-map tile styles | CDN |
| 🛣️ Geoapify | Truck driving-route API | `api.geoapify.com` |
| 🗄️ sql.js 1.10.3 | SQLite database in browser | cdnjs |
| 🅰️ Font Awesome 6.4 + Inter | Icons & typography | CDN |

---

## ✅ Notes & Limitations

- **Modern browser** recommended (Chrome, Edge, Firefox, Safari).
- KML export prefers the **File System Access API** (Chrome/Edge) with an automatic download fallback elsewhere.
- Preferences and app settings persist in `localStorage`.
- Routes persist in **IndexedDB** via sql.js. Clearing browser site data will delete saved routes.
- The public Geoapify routing service is rate-limited — when unavailable, the app falls back to approximate straight-line distances (labelled *Approximate*).
- TGA logic follows common KSA driving-hours rules, with all thresholds adjustable in **Settings** — always verify against official **Transport General Authority (TGA)** guidance.
- Load weight presets and default parameters are fully configurable in **Settings**.
- Internet is required for map tiles and live routing. Saved routes and settings are stored locally in the browser and persist across sessions, but full functionality requires connectivity.

---

## 🙌 Credits

- Map tiles © OpenStreetMap contributors, Esri, CARTO
- Routing powered by [Geoapify](https://www.geoapify.com/)
- Icons by Font Awesome · Mapping library by [Leaflet](https://leafletjs.com/)

---

© 2026 Bernando Jr Minguita. All rights reserved.
