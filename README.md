# Connecting Paths

A single-file browser tool: load a KML or KMZ, see its point placemarks as nodes on a map,
click node pairs to connect them (paths are auto-named from a configurable template,
`FROM↔TO` by default), add or remove nodes by hand, and export the results as CSV
(and as a KML with the paths drawn using the exact node coordinates).

## Use it

1. Double-click `index.html` (opens in your default browser — Chrome/Edge/Firefox).
2. Click **Load KML/KMZ…** or drag a `.kml` or `.kmz` file onto the window. `sample.kml` is included for testing.
3. Click **Add path** in the tool palette (it turns on and the cursor becomes a crosshair).
4. Click a node on the map (or in the Nodes list) — it turns orange. Click a second node.
5. The path is created immediately and named from the path template — `FROM↔TO` by default
   (the two node names in upper case, joined by `↔`, no spaces). A line is drawn between
   the two nodes.
6. Repeat for every pair (keep clicking to chain a route), then **Export CSV** and/or **Export KML**.

## Editing tools

A single row of tool buttons controls what clicking on the map does. Only one tool is
active at a time (radio-style); keyboard shortcuts switch tools instantly (ignored while
typing in a text field):

| Tool | Shortcut | What it does |
| --- | --- | --- |
| **Select** | `V` | Default tool. Click a node/path to select it, shift-click to add to the selection, or drag a box on empty map (or starting on a path) to select everything inside. |
| **Add node** | `N` | Click anywhere on the map to drop a new node. |
| **Add path** | `P` | Click a node, then another, to connect them; keep clicking to chain a route (`A→B→C→D`). Existing paths become click-through so a busy node stays easy to hit. |
| **Move** | `M` | Drag a node — or, if several are selected, drag any one of them — to reposition the whole group; connected paths follow in real time. |
| **Delete** | `D` | Click a node or path to remove it immediately. An inline **Undo** button appears in the status bar in case it was a mistake. |
| **Split path** | `S` | Click a path to insert a new node where you clicked and split it into two paths. |
| **Re-route** | `R` | Drag a path's endpoint onto a different node to rewire it; a click without dragging just selects the path as normal. |

`Esc` returns to Select (and clears the selection if you were already on it). With two
nodes selected (and no paths in the selection), **Merge nodes** combines them into one,
keeping whichever had more connections and dropping any duplicate/self links that would
result. `Delete`/`Backspace` removes the whole current selection in one step. **Snap**
(toggle button) pulls new/moved nodes onto an existing node within a few pixels, useful
for exact alignment.

Every node and path can also be edited precisely from the sidebar: **edit** on a node
opens a dialog with numeric latitude/longitude fields (validated to real-world ranges)
plus its name; **rename** on a path overrides its auto-generated name — that override is
remembered even if you later change the naming template or rename an endpoint node.

## Features

- Parses every `<Point>` placemark in the KML (folders and `MultiGeometry` included), using name + coordinates.
- Also parses any `<LineString>` placemarks in the file (e.g. the "Paths" folder from a
  previously exported KML) and rebuilds the corresponding connections automatically by
  matching each line's two endpoints back to nodes at the same coordinates (within 1 m).
- Connections are always pairs of nodes; duplicate pairs are rejected.
- **File styles**: the `<Style>`/`<StyleMap>` definitions in the loaded file are honored when
  drawing the network — `IconStyle` `<color>` and `<scale>` set each node's color and size,
  `<Icon><href>` is drawn as the actual marker image (icons packed inside a `.kmz` are
  extracted and used), and `LineStyle` `<color>`/`<width>` set each path's color and
  thickness. `StyleMap` follows the `normal` pair, and a `<Style>` written inline on a
  placemark wins over a `styleUrl`. The **File styles** button switches between the file's
  own appearance and the app's status palette (blue = unconnected, green = connected); it is
  disabled for files that define no styles. Selection always stays orange so the node you
  picked is obvious, and unstyled placemarks keep the app colors. Missing or unreachable
  icon images fall back to a plain circle instead of a broken image. Exporting KML writes
  these styles back out, so a load → export → load round-trip keeps the original look.
- Path names are generated, never typed, from a configurable template (default `FROM↔TO`, e.g. `TOWER A↔TOWER B`),
  unless you use **rename** on a path in the sidebar to set a custom name — that override sticks.
- **Multi-select**: shift-click nodes/paths to build up a selection, or drag a box (in the
  Select tool) to grab everything inside it. A multi-node selection can be dragged together
  in the Move tool, merged (if exactly two nodes with no paths between them are selected),
  or deleted in one step.
- **Undo/Redo** (Ctrl+Z / Ctrl+Shift+Z or Ctrl+Y) step back and forward through the whole
  editing history — connections, node adds/edits/deletes, merges, splits, re-routes,
  **Clear links** and CSV imports — up to 50 steps. Deletes are frictionless: instead of a
  confirmation prompt, the node/path is removed immediately and an inline **Undo** button
  appears in the status bar for a few seconds.
- **edit**/**rename** actions on every row in the Nodes/Connections list: node **edit** opens
  a dialog with numeric, range-validated latitude/longitude fields plus the name; renaming a
  node updates any of its connections that haven't been manually renamed; path **rename**
  sets a custom name that survives future template or endpoint-name changes; **del** removes
  the row (with the same frictionless Undo toast as the Delete tool).
- **Naming…** dialog: set the auto-naming format for future nodes and for all paths.
  Placeholders: `{n}`/`{n2}`/`{n3}`/`{n4}` (sequential number, optionally zero-padded);
  the path template also accepts `{from}`/`{to}` (node names as typed) and
  `{FROM}`/`{TO}` (upper-cased). Changing the **path** format immediately renames every
  existing connection; changing the **node** format only affects nodes added afterwards
  (existing/imported node names are left alone). **Reset to defaults** restores
  `Node {n}` / `FROM↔TO`; **Cancel** discards unsaved edits.
- Great-circle length per path and total length in the status bar.
- **Zoom to fit** (or press `F`) frames every node in view.
- **Dashboard** button: summary cards (nodes, connections, total/average/min/max length,
  average connections per node, unconnected nodes) plus three interactive charts — path
  length distribution, node degree distribution, and the top 10 most-connected nodes.
  Updates live as you add or remove connections.
- Basemaps: Streets (Esri, default) and Satellite (Esri), plus **Satellite (Mapbox)…** as a
  second, independently-sourced satellite option (useful since Mapbox and Esri refresh their
  imagery on different schedules, so one sometimes has a newer capture than the other for a
  given area), **Custom tile server…**, or none. Kept deliberately to just these, since the
  Esri layers are the ones proven reliable on restrictive/corporate networks; Carto and the
  main `openstreetmap.org` tile server were dropped for the same reason (see Notes below).
  Picking **Satellite (Mapbox)…** the first time prompts for a free Mapbox access token
  (from account.mapbox.com/access-tokens, no credit card required) which is stored only in
  this browser's `localStorage`.
  The choice is remembered. On load — and whenever you click **Recheck maps** — the app
  probes every provider for real reachability on your current network and disables/labels
  any that fail as "(unavailable)"; if your active basemap goes unreachable it automatically
  switches you to a working one and tells you which. (Mapbox Satellite is verified instead
  by its own tile-load-error check, like Custom tile server, since it needs a token first.)
- **Node labels** and **Path labels** toggle independently, plus a metric scale bar; both
  choices are remembered. A **"Hide node labels containing…"** text filter (next to the
  Node labels button) additionally hides just the labels of nodes whose name contains that
  text (case-insensitive) — handy for silencing one category of node (e.g. everything named
  like `P-01`) without turning off labels for every other node. It only affects label
  visibility, not the nodes themselves; leave it empty to show all node labels again.
- Work is auto-saved in the browser's local storage, so a reload restores your session.
- **Import CSV** re-loads a previously exported file and rebuilds the lines by matching
  coordinates (within 1 m) and falling back to node names; path names are regenerated
  from the current path template.

## CSV format

```
path_name,from_name,from_lat,from_lon,to_name,to_lat,to_lon,length_m,source_kml
```

Coordinates are written with 8 decimals, exactly as read from the KML, so the exported
paths share the same coordinates as the original nodes.

If you export before drawing any connections, a nodes-only CSV is written instead:

```
node_name,lat,lon,alt_m,connections,source_kml
```

## Exported KML

Contains a `Nodes` folder (the original points) and a `Paths` folder with one
`LineString` placemark per connection, named `FROM↔TO`. Opens directly in
Google Earth or QGIS. Loading this file back into the app (via **Load KML/KMZ…**
or drag-and-drop) restores both the nodes and the connections.

Styles read from the source file are re-emitted as `<Style>` blocks and referenced per
placemark, so colors, icon scales, icon hrefs and line widths survive the round-trip.
Nodes and paths that had no style (including anything you added in the app) use the
default orange `nodeStyle`/`pathStyle`. This happens regardless of the **File styles**
button, which only controls what is drawn on screen. Note that an exported `.kml` is a
plain file, not an archive, so icon `href`s that pointed inside a `.kmz` are written out
unchanged and will only resolve if those images sit next to the exported file.

## Notes

- Internet access is only needed for map tiles. Leaflet is bundled inside `index.html`, so
  the app itself works offline — pick **No basemap** if you have no connection, and node
  placement, linking, and both exports keep working normally.
- Corporate/restricted networks often allow some tile hosts (e.g. Esri's arcgisonline.com)
  while blocking others. The app detects this itself: unreachable providers are greyed out
  and marked "(unavailable)" in the dropdown, and clicking **Recheck maps** re-tests them
  all — useful after connecting to a VPN or changing networks. For an internal tile server,
  choose **Custom tile server…** and paste
  an XYZ template such as `https://tiles.mycompany.local/{z}/{x}/{y}.png`; it is saved for
  next time.
- `.kmz` files are unzipped in the browser (no upload, no extra libraries) and the KML inside
  is loaded — `doc.kml` is preferred, otherwise the shallowest `.kml` entry in the archive.
  Image files in the archive are extracted too, so `IconStyle` icons display; everything else
  (overlays, nested network links) is ignored. Very old browsers without `DecompressionStream`
  cannot inflate compressed KMZ; unzip the file manually there.
- The browser remembers the last session, including node/path colors and sizes. Icons that
  came from a `.kmz` are the one exception — they live only in memory, so after a reload
  those nodes fall back to colored circles until the `.kmz` is loaded again.
- A BOM or leading whitespace in the KML is handled automatically.
