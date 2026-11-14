Tour de Mars 🪐
<p align="center">
  <img src="./favicon.png" alt="Tour de Mars astronaut cat mascot" width="240" />
</p>
Explore Mars. Plan the walk. Understand the risk.
Tour de Mars is a web-based Mars exploration and extravehicular activity (EVA) planning prototype by Team 6bit_Larry, developed for the 2026 NASA Space Apps Challenge.
Challenge: Interplanetary Survival Guide: Martian Map  
Live Demo: https://tour-de-mars.vercel.app/  
MarsWalk Planner (Live): https://tour-de-mars.vercel.app/marswalk.html  
240s Presentation Video: https://youtu.be/hznaf08VCDU  
Source Repository: https://github.com/pengvil/Tour-de-Mars
> **Deployment note:** The live application is hosted on Vercel. For local execution and offline demonstration, follow the instructions in the [Getting started](#getting-started) section below.
Overview
What if astronauts could plan a Marswalk using genuine spacecraft imagery, compare possible routes, identify scientific destinations, and estimate whether their modeled oxygen reserve would allow a return to base?
Tour de Mars explores that question through a set of connected experiences:
MarsWalk: an interactive EVA mission-planning interface centered on Jezero Crater.
Mars Atlas: a global visual map of notable Martian locations.
Field Notes: research-based explorations of Jezero Crater, Gale Crater, Olympus Mons, and Valles Marineris.
Data Audit: source attribution, computational assumptions, limitations, and reproducibility information.
The project combines official NASA/JPL map imagery, an optional USGS/NASA CTX elevation-data pipeline, and our own route-planning and life-support scenario models. It is an educational and research prototype, not an operational astronaut navigation system.
Features
Feature	What it does
NASA Mars Trek map	Displays Jezero HiRISE and CTX orbital mosaics and global MOLA/HRSC context using real WMTS tiles.
Interactive mission points	Choose a base camp, add science stops, inspect map coordinates, and work inside a 5 × 5 km Jezero planning area.
Terrain processing	When the official CTX digital terrain model is loaded, derives elevation, slope, relief, coverage, and terrain-cost summaries.
Four route objectives	Compare Fast, Safe, Balanced, and Science routing with different optimization weights.
Oxygen/walkback model	Estimates scenario-dependent walking time, oxygen usage, and return-to-base reserve.
Route comparison and replay	Inspect alternatives, replay the planned traverse, and examine model outcomes.
Export/report	Export mission-planning results and inspect the reported source and model assumptions.
Research content	Explore planetary geology through the Mars Atlas and scientific Field Notes.
How the route planner works
When verified CTX terrain data is available, MarsWalk divides a 5 km × 5 km planning zone into 50 × 50 cells (2,500 cells, each 100 m wide). Its terrain-processing workflow calculates cell-level summaries from the elevation raster and supports an eight-neighbor route graph.
The implementation uses the following disclosed rules and assumptions:
Terrain summaries include slope and relief. A cell is considered passable when it meets the configured coverage and slope thresholds (at least 80% elevation coverage and 90th-percentile slope ≤ 25° in the documented version).
Routing adjusts cost weights depending on whether the user prioritizes time, modeled terrain safety, balance, or contextual scientific interest.
The Science objective currently favors named mission-interest waypoints. It does not analyze measured CRISM mineral abundance.
The oxygen/walkback model uses user-adjustable scenario assumptions, not NASA flight-certified consumption rates or operational limits.
Important: A route is not terrain-validated just because a NASA satellite image is visible beneath it. Measured elevation and slope require successful loading and processing of the official CTX DTM. If a demo build offers an explicitly labeled illustrative/map-only rehearsal, those results demonstrate the interface and assumptions—not measured slopes, terrain hazards, or certified safe routes.
NASA data and scientific provenance
Source	How the project uses it	Status
NASA/JPL Mars Trek — WMTS service documentation	Live orbital imagery tiles inside the custom MarsWalk and Atlas renderers.	Direct integration
MRO HiRISE — Jezero controlled orthomosaic	Detailed orbital image basemap for Jezero exploration.	Direct WMTS map layer
MRO CTX — Jezero controlled orthomosaic	Wider-area Jezero orbital image basemap.	Direct WMTS map layer
MOLA / Mars Express HRSC blended color-shaded product	Global Mars visual and relief context; the HRSC component originates with ESA's Mars Express mission.	Direct WMTS map layer
USGS/NASA Mars 2020 Jezero CTX DTM	Numerical elevation input for slope, passability, relief, and terrain-aware routing when loaded.	Supported dataset; not bundled in the repository
USGS/NASA interactive geologic maps	Geologic context and interpretation.	Reference / external map
Perseverance raw-image gallery	Ground-level Jezero context for research and visual comparisons.	Reference
Curiosity's proposed route up Mount Sharp	An example of presenting annotated traverses and science destinations; not route input for Jezero.	Design/reference
NASA Worldview	Earth-observation visualization reference; not a Mars imagery/terrain/weather service.	Reference only
> **What is not yet a measured route input:** CRISM mineral abundance, THEMIS thermal hazards, and Perseverance MEDA weather data. These may be discussed as scientific context or future integrations, but the current routing engine must not be described as processing them.
NASA/JPL, USGS, and ESA are credited for their respective data products. Tour de Mars is an independent Space Apps project; its presence in the challenge does not imply NASA or agency endorsement. Respect the original hosts' licensing, attribution, and data-use requirements.
Getting started
You can explore the project immediately via the live web deployment or run it locally:
Live Website: https://tour-de-mars.vercel.app/
MarsWalk Mission Control: https://tour-de-mars.vercel.app/marswalk.html
240-Second Presentation Video: https://youtu.be/hznaf08VCDU
The site is a static HTML/CSS/JavaScript application. There is no build step and nothing to install to run the website itself.
1. Run locally
Requirements: Git, a modern browser (Chrome, Edge, Firefox), an internet connection (the map tiles stream from NASA Mars Trek), and any one of Python 3, Node.js, or VS Code for the local server.
Clone the repository and start a local server from the project root (the folder that contains `index.html`):
```bash
git clone https://github.com/pengvil/Tour-de-Mars.git
cd Tour-de-Mars
```
Then use one of these:
```bash
# Option A: Python (macOS/Linux)
python3 -m http.server 8080

# Option A: Python (Windows)
py -m http.server 8080

# Option B: Node.js
npx serve -l 8080 .
```
Option C: VS Code. Install the Live Server extension, open the `Tour-de-Mars` folder, right-click `index.html`, and choose Open with Live Server.
Open http://localhost:8080 (Live Server uses its own port, usually 5500) and select MarsWalk, or go straight to http://localhost:8080/marswalk.html.
Use a local HTTP server rather than double-clicking the HTML files; opening them directly via `file://` can block scripts and data loading. To stop the server, press `Ctrl + C` in the terminal.
The map imagery loads from NASA Mars Trek over the internet. Browser-based terrain loading may additionally require access to the USGS/PDS GeoTIFF host and the GeoTIFF.js module CDN.
2. Enable verified terrain routing (recommended)
The repository's default version initializes `data/terrain-grid.js` to `null`; it does not contain real elevation cells. The reliable way to prepare scientific-terrain demonstrations is to preprocess the official CTX GeoTIFF and bundle the resulting grid.
Install the preprocessing dependencies:
```bash
python -m pip install -r requirements-terrain.txt
```
Download the official Jezero CTX DTM GeoTIFF to your computer, then run from the repository root (`Tour-de-Mars/`):
```bash
python tools/build_terrain.py "/path/to/JEZ_ctx_B_soc_008_DTM_MOLAtopography_DeltaGeoid_20m_Eqc_latTs0_lon0.tif"
```
This generates a derived grid in `data/terrain-grid.js` for the 50 × 50-cell planning zone. Use only a genuine CTX DTM as input. Verify the generated grid and MarsWalk's real-terrain status before treating slope or route results as CTX-derived. The generated small grid can be committed with the static website to reduce dependence on a large live download during judging.
3. Alternative terrain-loading methods
If the build supports them, MarsWalk also provides:
Retry official DEM: request the CTX GeoTIFF from the configured USGS/PDS-hosted endpoint and process it in the browser using GeoTIFF.js.
Load local GeoTIFF: choose a previously downloaded official CTX DTM from disk when cross-origin or range requests prevent online loading.
If terrain loading fails, the orbital map may still render, but verified terrain-based route calculations should not be claimed. Presentation builds with an illustrative mode must clearly retain their non-verified label in the UI and exports.
Project structure
```text
.
├── index.html                 # Project homepage and team
├── marswalk.html              # EVA planning interface
├── marswalk.js                # Mars Trek tiles, terrain handling, routing, replay
├── marswalk.css               # MarsWalk styles
├── atlas.html                 # Mars Atlas
├── atlas.js                   # Global Mars Trek imagery and map behavior
├── data-audit.html            # Data sources and limitations
├── field-jezero.html          # Jezero Crater field note
├── field-gale.html            # Gale Crater field note
├── field-olympus.html         # Olympus Mons field note
├── field-valles.html          # Valles Marineris field note
├── assets/                    # Local site imagery and team assets
├── data/
│   └── terrain-grid.js        # Optional derived verified CTX grid
├── tools/
│   └── build_terrain.py       # Offline terrain preprocessing
├── requirements-terrain.txt   # Python terrain-preprocessing dependencies
└── README.md
```
The exact directory layout of the GitHub `main` branch may differ slightly from the tree above. The README lives at the repository root, alongside `index.html`.
Technologies
Frontend: HTML5, CSS3, vanilla JavaScript, Canvas-based interactive mapping.
NASA imagery: NASA/JPL Mars Trek Web Map Tile Service (WMTS).
Numerical terrain input: USGS/NASA Mars 2020 CTX Digital Terrain Model (GeoTIFF).
Raster processing: GeoTIFF.js in the browser; Rasterio and NumPy for offline preprocessing.
Version control and deployment: Git/GitHub and Vercel.
Planning algorithms: custom JavaScript terrain-cost route search, waypoint objectives, and modeled oxygen walkback checks.
Team — 6bit_Larry
Member	Project role
MD. Zahidul Islam Navil	Team Leader & Developer
Shuaib Mahfooz	Researcher & Developer
Kazi Shafiqul Alam	Researcher & Developer
Sayed Shahriar Mustafa	Research Assistant
Shihab Uddin Bhuiyan	QA & Testing Assistant
Md. Ishtiaque Hossain	QA & Testing Assistant
The roles above describe the team's areas of work; they are not intended to claim individual authorship of particular source files or scientific findings without an accompanying contribution record.
AI assistance and transparency
OpenAI ChatGPT was used as a development-assistance tool for research organization, code drafting and review, UI iteration, troubleshooting, documentation, technical explanations, and presentation preparation. Representative requests included reviewing organizer-provided NASA resources, assessing whether the custom map should be replaced by Mars Trek, and designing a more reliable demonstration.
AI assistance was applied to public scientific descriptions, project source code, draft content, and team-provided instructions. AI outputs are not NASA measurements, are not independently validated scientific results, and are not used as a live generative-AI route engine. Any additional AI tools used by individual team members should be disclosed before final submission.
The Field Notes are AI-assisted literature syntheses, not records of physical field visits. Their source papers and limitations are listed on the individual pages.
Scientific references
Selected peer-reviewed papers already cited by the site's Field Notes:
Brown, A. J., Viviano, C. E., & Goudge, T. A. (2020). Olivine-Carbonate Mineralogy of the Jezero Crater Region. Journal of Geophysical Research: Planets.
Mangold, N., et al. (2021). Perseverance rover reveals an ancient delta-lake system and flood deposits at Jezero crater, Mars. Science.
Farley, K. A., et al. (2022). Aqueously altered igneous rocks sampled on the floor of Jezero crater, Mars. Science.
Grotzinger, J. P., et al. (2014). A habitable fluvio-lacustrine environment at Yellowknife Bay, Gale crater, Mars. Science.
Bleacher, J. E., et al. (2007). Olympus Mons, Mars: Inferred changes in late Amazonian aged effusive activity from lava flow mapping of Mars Express High Resolution Stereo Camera data. Journal of Geophysical Research: Planets.
Andrews-Hanna, J. C. (2012). The formation of Valles Marineris: 1. Tectonic architecture and the relative roles of extension and subsidence. Journal of Geophysical Research: Planets.
These papers support the scientific background in the Field Notes; they are not directly ingested by MarsWalk's numerical route planner. Additional mission archives, publications, map credits, and image sources are documented on the relevant Field Note pages and in the Data Audit.
Scope, limitations, and roadmap
Current scope: a proof-of-concept map and EVA planning application. NASA imagery is authentic; terrain-dependent analysis is authentic only when the official CTX DTM has been successfully processed. Oxygen, pace, science-interest scoring, and route weights are scenario or heuristic models.
Potential next steps:
Validate the CTX-derived terrain grid and route outputs against reference terrain profiles and repeatable test cases.
Integrate additional verified science/environmental layers (for example CRISM, THEMIS, and MEDA) only with correct data lineage, resolution, units, and timestamps.
Improve operational constraints such as communications, illumination, EVA duration, return reserves, and uncertainty.
Add broader automated browser tests, accessibility checks, and mobile QA.
Safety statement: Tour de Mars is not a flight-certified EVA navigation tool, a Mars weather service, or medical/life-support guidance.
Credits and usage
Planetary images, mosaics, elevation datasets, and mission descriptions retain their NASA/JPL, USGS, ESA, and original instrument-team attributions.
Third-party photography, graphics, templates, and other assets must be used only within their applicable license/permission terms and credited to their original creators.
NASA and the NASA Space Apps Challenge do not endorse this independent student project.
The public source repository and its assets may have different reuse terms. Do not assume the project has a software license unless the repository includes a `LICENSE` file.
---
Built by Team 6bit_Larry for the 2026 NASA Space Apps Challenge — The Next Frontier.