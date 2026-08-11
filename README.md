# MyTX15Map

Interactive map of the **new 2026 Texas Congressional District 15**, published as a
static GitHub Pages site and embedded on the campaign landing page at
[pulidoevents.com/mytx15](https://pulidoevents.com/mytx15).

**Live:** https://infobp.github.io/MyTX15Map/

---

## Source of the district geometry

The boundary is the enacted 2025 Texas congressional plan **PLANC2333**, published by
the **Texas Legislative Council**.

`tx15.geojson` in this repo is a **web-optimized rendering of District 15 only** —
reprojected to WGS84 / EPSG:4326 and simplified for display. It is not a substitute
for the official plan files and should not be used for legal, analytical, or
redistricting purposes. Always cite the Texas Legislative Council for the
authoritative boundary.

Regenerated from the source shapefile with:

```bash
ogr2ogr \
  -f GeoJSON \
  -t_srs EPSG:4326 \
  -simplify 0.0001 \
  -lco COORDINATE_PRECISION=5 \
  tx15.geojson \
  "/Users/marco/Documents/Web/PLANC2333/PLANC2333.shp" \
  -where "District = 15"
```

That source path is a **local developer directory on Marco's machine**; the raw
shapefile is deliberately not in this repo. Verify any regenerated file with:

```bash
ogrinfo -al -so tx15.geojson    # expect: Feature Count: 1, District = 15, EPSG:4326
```

---

## File structure

```
MyTX15Map/
├── index.html        the whole map — markup, styles, and logic in one file
├── tx15.geojson      District 15 boundary, simplified (288 KB, 1 feature)
├── .nojekyll         stops GitHub Pages running Jekyll over the files
├── .gitignore
├── README.md
└── icons/            11 city SVGs, one per labeled community
```

No build step, no bundler, no framework, no package.json. What is in the repo is
exactly what the browser runs.

---

## Local testing

```bash
cd "/Users/marco/Documents/Web/MyTX15Map"
python3 -m http.server 8000
```

Open http://localhost:8000 and check:

- the district renders and fills most of the frame
- all 11 icons and city names appear, with even icon-to-name spacing
- no basemap city labels, no district number inside the polygon
- resizing the window refits the district and rescales the icons
- no console errors, no 404s

The Python server is for local testing only — it is not part of production.

---

## Updating and deploying

Normal changes (styling, city list, icons, regenerated geometry):

```bash
git add .
git commit -m "Update TX-15 map"
git push
```

GitHub Pages redeploys automatically within about a minute. **The public URL never
changes**, so the iframe on pulidoevents.com does not need editing after a normal
update — that is the main reason this lives in its own repo.

Pages is configured as: Deploy from a branch → `main` → `/ (root)`.

---

## Dependencies and attribution

- [Leaflet 1.9.4](https://leafletjs.com/) via unpkg, pinned with Subresource Integrity
- Basemap tiles: **CARTO** *light_nolabels* — © OpenStreetMap contributors © CARTO
- District geometry: **Texas Legislative Council**, plan PLANC2333
- City icons: SVG Repo

**No API key is required.** Nothing here is authenticated, rate-limited by us, or
dependent on any server we run.

---

## What must never be committed here

This repository is **public**. It holds public map assets only. Never commit:

- voter files, voter records, or any personal information
- credentials, API keys, tokens, or `.env` files
- Secretary of State FTP details
- private app configuration or internal campaign notes

`.gitignore` covers `.env*`, `*.log`, and `.DS_Store`, but it is a convenience, not a
safety net — check `git status` before every commit.
