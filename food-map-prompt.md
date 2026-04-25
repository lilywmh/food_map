# Food Map — Build Prompt

Build an interactive food map web app as a single HTML file.

---

## Visual Style

Watercolor travel journal aesthetic. Use Stamen Watercolor tile layer via Leaflet.js. Base background `#f5efe6`. Color palette: warm oatmeal, brick tea (`#9b6e52`), moss green (`#6e8c72`), dusty lavender (`#8a7ba0`), old paper (`#f0e8dc`). Font: load **Klee One** and **Zen Maru Gothic** from Google Fonts. Klee One for titles, place names, badges. Zen Maru Gothic for body. All UI should feel like a handwritten travel journal — warm, quiet, personal.

---

## Map

- Leaflet.js via CDN
- Stamen Watercolor tiles: `https://stamen-tiles.a.akamaihd.net/watercolor/{z}/{x}/{y}.jpg`
- Custom circular markers, colored by cuisine type: ramen `#c17a5e`, cafe `#7a9e87`, sushi `#7a8fb5`, sweets `#b87aa0`
- On marker click: highlight marker with glow ring, update sidebar card
- On region pill click: map flies to that region's bounding box and filters markers

---

## Geo Clustering — Auto Region Detection

On page load, automatically cluster restaurant data into regions using a simple distance-based algorithm:

- Group restaurants whose coordinates are within ~2km of each other into one cluster
- Name each cluster by the `area` field of the restaurant closest to the cluster centroid
- Generate region filter pills dynamically from clusters, format: `地区名 · N家`
- Store each restaurant's `clusterId` after clustering
- Region pills appear in a first filter row above cuisine/budget filters
- Clicking a region pill: map `fitBounds` to that cluster's bounding box + filters to only that cluster's markers
- "全部" pill always present to reset region filter

---

## Filter Bar

Two rows of floating pill filters:

- **Row 1** (auto-generated): Region pills from geo clustering + "全部" reset pill
- **Row 2**: Cuisine type multi-select pills + budget pills (便饭 / 犒劳自己 / 值得庆祝 mapping to $ / $$ / $$$) + "✦ 随机带我去" dashed button on the right
- All filters combine with AND logic: active region + active cuisines + active budget all apply simultaneously
- Active pills: filled with corresponding color. Inactive: transparent with border.

---

## Restaurant Card (Sidebar)

Sidebar fixed to right, 230px wide. On marker click, card updates with smooth content transition. Card contains exactly:

- Area label (small caps, muted)
- Restaurant name (Klee One, large)
- Recommended dish (moss green, italic)
- Budget level with label: `$` → 便饭, `$$` → 犒劳自己, `$$$` → 值得庆祝
- Rating as ♥ symbols (filled + empty, dusty pink)
- Note toggle button with dashed border, arrow rotates on open, slides open with CSS transition (max-height animation)
- Note content in a warm inset box with left accent border, italic Klee One

---

## Data

Hardcode 10 sample restaurants across 3 cities (Tokyo, Kyoto, Osaka), spread across at least 5 distinct geographic clusters. Each object:

```js
{ name, city, area, cuisine, budget, rating, dish, note, lat, lng }
```

Ensure `lat`/`lng` values are geographically accurate and spread enough that the clustering algorithm produces meaningful groups.

---

## Technical Requirements

- Single HTML file, no build tools, no frameworks
- All dependencies via CDN (Leaflet 1.9, Google Fonts)
- Clustering runs client-side on page load, pure JS, no external clustering library needed (implement simple greedy distance grouping)
- Filters are reactive: any change immediately updates visible markers and card state
- Mobile responsive: on small screens, sidebar collapses below map
- Clean modular JS: data array → clustering function → render function → event handlers, clearly separated
