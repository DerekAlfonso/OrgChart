# Org Chart Editor

An interactive, browser-based org chart editor. Open `org-chart.html` in any modern browser — no server, no install, no dependencies beyond an internet connection for the D3 library.

---

## What the tool does

- **Visualises** organisational hierarchies as a balanced, auto-laid-out tree
- **Edits** nodes inline — title, description, connection style, and colour
- **Restructures** the chart by dragging a node onto any other node to reparent it
- **Saves** the current chart to a `.json` file and **loads** any previously saved chart back in
- **Starts fresh** with a blank root node or loads from one of the configured preset files
- Works entirely in the browser — all data stays on your machine

---

## How the tool works

### Loading a chart

When you open `org-chart.html` you are greeted by an empty-state screen with three options:

| Option | How |
|---|---|
| **Preset tile** | Click a named tile (e.g. *Org Chart – Now*) to fetch that JSON file automatically |
| **Browse for JSON file** | Opens a file picker so you can load any `.json` chart from your computer |
| **Start from scratch** | Creates a single blank root node and opens the edit dialog immediately |

Once a chart is loaded the canvas appears and all editing tools become active.

### Navigating the canvas

| Action | How |
|---|---|
| Zoom in / out | Scroll wheel, or pinch on a trackpad |
| Pan | Click and drag on the background |
| Fit to screen | Click **⊠ Fit** in the toolbar |

### Editing nodes

| Action | How |
|---|---|
| **Edit** a node | Double-click it, or select it and press **Enter**, or use **✏️ Edit** in the toolbar |
| **Add a child** | Select a node and click **＋ Add Child**, or right-click → *Add Child Node* |
| **Add a sibling** | Select a node and click **＋ Add Sibling**, or right-click → *Add Sibling Node* |
| **Delete** a node | Select it and press **Del / Backspace**, or click **🗑 Delete** in the toolbar |
| **Move / reparent** | Drag a node and drop it onto the new parent — the target highlights green when valid |
| **Collapse / expand** | Right-click a node → *Collapse / Expand* (collapsed nodes show a badge with the child count) |

The **Edit Node** dialog lets you change:
- **Title** — the bold name displayed on the node
- **Description** — a smaller subtitle line (up to two wrapped lines)
- **Link Style** — *Solid* (direct report) or *Dashed* (advisory / no formal authority)
- **Node Colour** — one of nine colour themes (see below)

### Saving and loading

- **💾 Save JSON** — downloads the current chart as `org-chart.json` and also shows the raw JSON in a modal so you can copy it
- **📂 Load JSON** — opens a file picker; the JSON is previewed before it replaces the current chart

The saved JSON is plain text and fully round-trips: load → edit → save → reload produces an identical chart.

---

## How to configure the tool

All configuration lives in a single block near the top of the `<script>` section in `org-chart.html`:

```js
// ★ PRESET FILES CONFIG
const PRESET_FILES = [
  { name: "Org Chart – Now",    url: "Org Chart - Now.json"    },
  { name: "Org Chart – Future", url: "Org Chart - Future.json" },
];
```

### `PRESET_FILES`

An array of preset chart definitions. Each entry has two fields:

| Field | Description |
|---|---|
| `name` | Display name shown in the toolbar dropdown and on the empty-state tile |
| `url`  | Path or URL to the JSON file. Can be a **relative path** (e.g. `./charts/my-chart.json`) or a full **`https://`** URL |

The presets are loaded via `fetch()` (XHR). If the array is empty, the preset dropdown and tiles are hidden automatically.

**Adding a new preset:**
```js
const PRESET_FILES = [
  { name: "Org Chart – Now",      url: "Org Chart - Now.json"      },
  { name: "Org Chart – Future",   url: "Org Chart - Future.json"   },
  { name: "Engineering Only",     url: "charts/engineering.json"   },
  { name: "Remote team snapshot", url: "https://example.com/remote.json" },
];
```

### Layout constants

Further down the script, four constants control node sizing and spacing:

```js
const NW   = 172;  // Node width  (px)
const NH   = 70;   // Node height (px)
const HGAP = 44;   // Horizontal gap between sibling nodes (px)
const VGAP = 88;   // Vertical gap between parent and child rows (px)
```

Increase `HGAP` / `VGAP` to spread the tree out; decrease them to pack it tighter.

### Node colours

Nodes can be coloured using the `color` field in the JSON or the *Node Colour* dropdown. Available values:

| Value | Appearance |
|---|---|
| `default` | White background, light grey border |
| `indigo`  | Soft indigo tint — good for leadership / C-suite |
| `blue`    | Light blue — good for engineering teams |
| `teal`    | Teal — good for product / design |
| `green`   | Light green — good for growth / new roles |
| `purple`  | Soft purple — good for data / analytics |
| `orange`  | Warm orange — good for operations |
| `red`     | Light red — good for flagged / transitional roles |
| `gray`    | Neutral gray — good for advisory / external roles |

Coloured nodes also display a matching accent strip across the top of the node.

### Connection line styles

The `style` field on each node controls how its connection to its parent is drawn:

| Value | Meaning | Use for |
|---|---|---|
| `solid`  | Solid line (──) | Direct report / formal authority |
| `dashed` | Dashed line (╌╌) | Advisory relationship / no formal authority |

---

## JSON file format

Each chart is a single JSON object. The root object and every node share the same schema:

```json
{
  "id":          "unique-string",
  "title":       "Node Title",
  "description": "Optional subtitle",
  "style":       "solid",
  "color":       "default",
  "children": [
    { ... }
  ]
}
```

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | yes | Must be unique within the file |
| `title` | string | yes | Displayed in bold on the node |
| `description` | string | no | Displayed below the title in a smaller font |
| `style` | `"solid"` \| `"dashed"` | no | Defaults to `"solid"` |
| `color` | string | no | One of the colour values listed above; defaults to `"default"` |
| `children` | array | no | Nested child nodes; omit or use `[]` for leaf nodes |

---

## Files in this folder

| File | Description |
|---|---|
| `org-chart.html` | The self-contained editor application |
| `Org Chart - Now.json` | Current org structure (NOW state) |
| `Org Chart - Future.json` | Target org structure (FUTURE state, 12–24 months) |
| `Org Chart.png` | Original reference image the charts were built from |
| `Org Chart README.md` | This file |
