# Org Chart Editor

An interactive, browser-based org chart editor. Open `index.html` in any modern browser — no server, no install, no dependencies beyond an internet connection for the D3 library.

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

When you open `index.html` you are greeted by an empty-state screen with three options:

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

All configuration lives in a single block near the top of the `<script>` section in `index.html`:

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

## Deploying a public version via Cloudflare

The editor is a single self-contained HTML file with no server-side dependencies, which makes it trivial to host publicly. The two most straightforward options are a direct upload through the Cloudflare Dashboard or a CLI-based deploy with Wrangler.

### Option 1 — Cloudflare Dashboard (no CLI required)

1. **Open the Cloudflare Dashboard** at [dash.cloudflare.com](https://dash.cloudflare.com) and log in.
2. In the left sidebar go to **Workers & Pages**, then click **Create**.
3. Select the **Pages** tab and choose **Upload assets**.
4. Give your project a name (e.g. `my-org-chart`) and click **Create project**.
5. Drag and drop your `index.html` file (and any JSON preset files you want to serve alongside it) into the upload area, then click **Deploy site**.
6. Cloudflare will issue a `*.pages.dev` URL within seconds. You can add a custom domain later under the project's **Custom domains** tab.

Subsequent updates follow the same flow: open the project, go to **Deployments**, click **Create new deployment**, and upload the updated file.

### Option 2 — Wrangler CLI

Wrangler is Cloudflare's official CLI tool. This approach is better suited to a repeatable deploy pipeline or when you want to deploy directly from a terminal.

**Prerequisites**

- [Node.js](https://nodejs.org) 18 or later
- A [Cloudflare Dash](https://dash.cloudflare.com/) account

**Steps**

```bash
# 1. Install Wrangler globally
npm install -g wrangler

# 2. Authenticate with your Cloudflare account
wrangler login

# 3. From the folder containing index.html, deploy to Pages
wrangler pages deploy . --project-name=my-org-chart
```

On the first run Wrangler will create the Pages project automatically. Every subsequent run deploys a new version.

If you want a different HTML file name served at the root URL, you can tell Wrangler which file is the entry point by placing a `_redirects` file in the same folder:

```
/ /org-chart.html 200
```

**No `wrangler.toml` is needed** for a static Pages deployment. The CLI command above is sufficient. A config file is only required if you are deploying a Cloudflare Worker (server-side JavaScript), which this project does not use.

### Serving preset JSON files publicly

If your `PRESET_FILES` config references relative paths, upload those JSON files alongside `index.html` in the same deploy. Cloudflare Pages will serve them at the same origin, so the `fetch()` calls in the app will resolve correctly with no CORS issues.

---

## Files in this folder

| File | Description |
|---|---|
| `index.html` | The self-contained viewer / editor application |
| `README.md` | This file |
