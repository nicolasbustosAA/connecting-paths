# Connecting Paths

A single-file browser tool to turn KML/KMZ point placemarks into a connected node
network — click pairs to link them, then export as CSV, KML, or KMZ. No install, no
server, no account: everything runs locally in your browser.

## Getting started

1. Double-click `index.html` to open it in your browser (Chrome, Edge, or Firefox).
2. Click **Load KML/KMZ…**, or drag a `.kml`/`.kmz` file onto the window. `sample.kml`
   is included if you just want to try it out.
3. Click **Add path** in the tool palette (the cursor becomes a crosshair).
4. Click a node on the map (or in the Nodes list) — it turns orange — then click a
   second node. The path is created immediately and named automatically
   (`FROM↔TO` by default). Keep clicking to chain a route.
5. Click **Export…** and pick any combination of **CSV**, **KML**, and **KMZ**.

## Editing tools

One row of tool buttons controls what clicking on the map does. Only one is active at
a time; keyboard shortcuts switch instantly (ignored while you are typing in a text
field):

| Tool | Shortcut | What it does |
| --- | --- | --- |
| **Select** | `V` | Default tool. Click a node/path to select it, shift-click to add more, or drag a box on empty map to select everything inside it. |
| **Pan** | `H` | Drag anywhere to move the map around without selecting or moving anything — handy on a trackpad. |
| **Add node** | `N` | Click anywhere on the map to drop a new node. |
| **Add path** | `P` | Click a node, then another, to connect them; keep clicking to chain a route (`A→B→C→D`). |
| **Move** | `M` | Drag a node — or, with several selected, drag any one of them — to reposition the whole group; connected paths follow along. |
| **Delete** | `D` | Click a node or path to remove it immediately. An inline **Undo** appears in the status bar in case it was a mistake. |
| **Split path** | `S` | Click a path to insert a new node where you clicked and split it into two paths. |
| **Re-route** | `R` | Drag a path's endpoint onto a different node to rewire it. |

`Esc` returns to Select. With two nodes selected (and no paths), **Merge nodes**
combines them into one. `Delete`/`Backspace` removes the whole current selection in
one step. **Snap** (toggle button) pulls new/moved nodes onto an existing node when
you drop close by, useful for exact alignment.

Every node and path can also be edited precisely from the sidebar:

- Node **edit** opens a dialog with a name and numeric latitude/longitude fields.
- Path **rename** sets a custom name that sticks even if you later change the naming
  template or rename an endpoint node.
- **Add by coordinates…** creates a new node by typing a name and exact lat/lon
  instead of clicking the map — handy for placing a node at a surveyed position.
- Double-clicking a node on the map (Select tool) is a shortcut to rename it.

**Copy style** / **Paste style** let you match one node's look onto others: select a
node and **Copy style** grabs its color, opacity, icon scale and image; select any
other node(s) and **Paste style** applies it. All edits are undoable with Ctrl+Z.

### Bulk style

**Bulk style…** applies a color, opacity, icon scale, icon (nodes) or line width
(paths) to many nodes or paths at once. Choose what to target — **Nodes** or
**Paths** — then which ones:

- **All** — every node/path.
- **Current selection** — whatever is currently selected.
- **Name match** — Contains / Starts with / Ends with / Regex, with a "doesn't
  match" option to invert it (e.g. select every node whose name does *not*
  contain `p`).
- **Connection state** (nodes only) — Unconnected, Dead ends (1 path), or Hubs
  (3+ paths).

The dialog shows a live match count and highlights matches on the map as you adjust
the query. Only the properties you check are changed — leave others unchecked to keep
each item's existing look. **Reset to file style** clears your bulk edits and restores
the loaded file's original appearance.

**Icon** (nodes only) swaps the plain colored dot for a Google Maps-style pin, from a
preset palette of 12 colors (red, orange, yellow, green, teal, blue, indigo, purple,
pink, brown, gray, black) — no image files or internet connection needed. Pick the
**×** swatch to clear a node's icon back to a plain dot.

## Naming

Paths and new nodes are named automatically from a template you can customize via the
**Naming…** dialog:

- Placeholders: `{n}` / `{n2}` / `{n3}` / `{n4}` insert a sequential number (optionally
  zero-padded).
- The path template also accepts `{from}` / `{to}` (node names as typed) and
  `{FROM}` / `{TO}` (upper-cased) — default is `{FROM}↔{TO}`.
- Changing the **path** format renames every existing connection immediately.
  Changing the **node** format only affects nodes you add afterward.
- **Reset to defaults** restores `Node {n}` / `FROM↔TO`.

Renaming a path or node from the sidebar overrides the template for that item — the
override sticks even if you later change the template again.

## Viewing the network

- **Node labels** and **Path labels** toggle independently. A **"Hide node labels…"**
  filter (Contains / Starts with / Ends with) lets you silence labels for one category
  of node — e.g. everything starting with `TEMP-` — without hiding all labels.
- **Zoom to fit** (or press `F`) frames every node in view.
- The status bar shows total path length and per-path great-circle length.
- **File styles** toggles between a loaded file's own colors/icons and this app's
  status palette (blue = unconnected, green = connected). Selection always stays
  orange so the picked node is obvious.

### Dashboard

Click **Dashboard** for network-health cards: unconnected nodes, separate networks
(groups that are not linked to each other — should be 1), duplicate paths, and
overlapping nodes (likely accidental duplicates from an import), plus counts for
nodes, connections, total length, and the busiest node. Three charts show path length
distribution, node degree distribution, and the top 10 most-connected nodes. It
updates live as you edit.

## Basemaps

Choose from Streets (Esri, default), Satellite (Esri), Satellite (Mapbox), a custom
tile server, or no basemap at all. The app checks which providers your network can
actually reach and greys out the rest as "(unavailable)" — click **Recheck maps**
after changing networks or connecting to a VPN. If your active basemap becomes
unreachable it automatically switches you to a working one.

- **Satellite (Mapbox)** needs a free access token (get one at
  account.mapbox.com/access-tokens, no credit card required); you'll be prompted the
  first time you pick it.
- **Custom tile server…** accepts an XYZ URL template, e.g.
  `https://tiles.mycompany.local/{z}/{x}/{y}.png` — useful for an internal map server.
- **No basemap** works fully offline: node placement, linking, and exporting all keep
  working without any internet connection.

## Undo, redo, and autosave

Ctrl+Z / Ctrl+Shift+Z (or Ctrl+Y) step back and forward through your whole editing
history — up to 50 steps. Deletes never prompt for confirmation; instead, an inline
**Undo** button appears in the status bar for a few seconds after each one. Your work
is auto-saved in the browser, so reloading the page restores your session (loaded
`.kmz` icon images are the one exception — reload those files again if a page refresh
clears their icons).

## Loading files

- KML/KMZ files are read entirely in your browser — nothing is uploaded anywhere.
- Every point placemark becomes a node (including ones inside folders or grouped
  geometry); every line placemark (e.g. a "Paths" folder from a previous export)
  is read back in as a connection between the matching nodes.
- Colors, icons, and line styles defined in the file are shown automatically; use
  **File styles** to toggle them off in favor of this app's own palette.
- Duplicate connections between the same two nodes are automatically rejected.

## Importing CSV

**Import CSV** re-loads a previously exported CSV and rebuilds the connections,
matching nodes by coordinates (falling back to name if needed). Path names are
regenerated from the current naming template.

## Exporting

Click **Export…**, check any combination of **CSV**, **KML**, and **KMZ**, and click
Export. If you pick just one format, your browser may offer a native "save as"
dialog so you can choose the destination (supported in desktop Chrome/Edge);
otherwise, or if you pick more than one format, each file downloads to your normal
downloads folder.

- **CSV** — one row per connection (`path_name, from_name, from_lat, from_lon,
  to_name, to_lat, to_lon, length_m, source_kml`), or one row per node if you have not
  connected anything yet (`node_name, lat, lon, alt_m, connections, source_kml`).
  Coordinates are written with 8 decimals, matching the original file exactly.
- **KML** — a `Nodes` folder and a `Paths` folder with one line per connection,
  named `FROM↔TO`. Opens directly in Google Earth or QGIS, and loads straight back
  into this app with both nodes and connections intact. Colors, icons, and line
  styles are preserved in the round-trip.
- **KMZ** — the same KML, bundled as an archive together with any icon images that
  came from the original file, so custom icons keep working without extra steps.
  (If you loaded a plain `.kml`, or only used the built-in pin icons, KMZ export is
  equivalent to KML since there are no extra images to bundle.)

## Notes

- The app itself works fully offline — only the map tiles need an internet
  connection, so pick **No basemap** if you have none.
- Corporate or restricted networks sometimes block certain map tile providers; use
  **Recheck maps** after switching networks, or set up a **Custom tile server…**
  pointing at an internal source.
- Very old browsers may not be able to open compressed `.kmz` files — unzip them
  to `.kml` manually if loading one fails.