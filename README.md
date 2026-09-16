# Connecting Paths

A single-file browser tool: load a KML or KMZ, see its point placemarks as nodes on a map,
click node pairs to connect them (paths are auto-named from a configurable template,
`FROM↔TO` by default), add or remove nodes by hand, and export the results as CSV
(and as a KML with the paths drawn using the exact node coordinates).

## Use it

1. Double-click `index.html` (opens in your default browser — Chrome/Edge/Firefox).
2. Click **Load KML/KMZ…** or drag a `.kml` or `.kmz` file onto the window. `sample.kml` is included for testing.
3. Click **Add path** (it turns on and the cursor becomes a crosshair).
4. Click a node on the map (or in the Nodes list) — it turns orange. Click a second node.
5. The path is created immediately and named from the path template — `FROM↔TO` by default
   (the two node names in upper case, joined by `↔`, no spaces). A line is drawn between
   the two nodes.
6. Repeat for every pair, then **Export CSV** and/or **Export KML**.

## Features

- Parses every `<Point>` placemark in the KML (folders and `MultiGeometry` included), using name + coordinates.
- Also parses any `<LineString>` placemarks in the file (e.g. the "Paths" folder from a
  previously exported KML) and rebuilds the corresponding connections automatically by
  matching each line's two endpoints back to nodes at the same coordinates (within 1 m).
- Connections are always pairs of nodes; duplicate pairs are rejected.
- Path names are generated, never typed, from a configurable template (default `FROM↔TO`, e.g. `TOWER A↔TOWER B`).
- **Add path**: connections are only created while this toggle is on. While it is on,
  existing paths are click-through (non-clickable), so a node with many connections
  running over it stays easy to hit. With the toggle off, clicking a node just selects
  it and clicking a path highlights it. Press `Esc` (or click the button again) to
  leave the mode; **Add node** and **Add path** are mutually exclusive.
- **Add node**: click the button (it turns on, cursor becomes a crosshair), then click
  anywhere on the map to drop a new node there, named from the node template. Click the
  button again (or press `Esc`) to leave add mode.
- **Rename**/**del** buttons on every row in the Nodes list: rename updates any of that
  node's existing connection names to match; delete removes the node and, after
  confirming, any connections attached to it.
- **Naming…** dialog: set the auto-naming format for future nodes and for all paths.
  Placeholders: `{n}`/`{n2}`/`{n3}`/`{n4}` (sequential number, optionally zero-padded);
  the path template also accepts `{from}`/`{to}` (node names as typed) and
  `{FROM}`/`{TO}` (upper-cased). Changing the **path** format immediately renames every
  existing connection; changing the **node** format only affects nodes added afterwards
  (existing/imported node names are left alone). **Reset to defaults** restores
  `Node {n}` / `FROM↔TO`; **Cancel** discards unsaved edits.
- Great-circle length per path and total length in the status bar.
- Delete any connection, **Undo** (Ctrl+Z), **Clear links**, `Esc` cancels a pending selection.
- **Dashboard** button: summary cards (nodes, connections, total/average/min/max length,
  average connections per node, unconnected nodes) plus three interactive charts — path
  length distribution, node degree distribution, and the top 10 most-connected nodes.
  Updates live as you add or remove connections.
- Basemaps: Streets (Esri, default) and Satellite (Esri), plus **Custom tile server** or
  none. Kept deliberately to just the two Esri layers, since they're the ones proven
  reliable on restrictive/corporate networks; Carto and the main `openstreetmap.org` tile
  server were dropped for the same reason (see Notes below).
  The choice is remembered. On load — and whenever you click **Recheck maps** — the app
  probes every provider for real reachability on your current network and disables/labels
  any that fail as "(unavailable)"; if your active basemap goes unreachable it automatically
  switches you to a working one and tells you which.
- Labels toggle for node and path names, plus a metric scale bar.
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

## Exported KML

Contains a `Nodes` folder (the original points) and a `Paths` folder with one
`LineString` placemark per connection, named `FROM↔TO`. Opens directly in
Google Earth or QGIS. Loading this file back into the app (via **Load KML…**
or drag-and-drop) restores both the nodes and the connections.

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
  Other contents of the archive (images, overlays, styles) are ignored. Very old browsers
  without `DecompressionStream` cannot inflate compressed KMZ; unzip the file manually there.
- A BOM or leading whitespace in the KML is handled automatically.
