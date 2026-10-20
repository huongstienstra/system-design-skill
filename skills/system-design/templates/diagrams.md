# Drawing diagrams

Each diagram is a hand-written SVG file that the doc embeds as an image. The reader sees it right there in the doc; there's no code to paste anywhere. Three kinds:

| Kind | Step | File |
|---|---|---|
| Architecture | 4 | `<name>-architecture.svg` |
| Class (data model) | 3 | `<name>-classes.svg` |
| Sequence | 5, one per hard part | `<name>-sequence-<topic>.svg` |

## Rules
- **Background**: the first element is a white `rect` covering the whole canvas. Without it, the drawing disappears on dark themes.
- **Size**: `width` and `height` equal to the `viewBox`. 960 wide at most; height to fit.
- **Layout**: on a grid. Architecture flows left to right (clients, our services, data stores, external services); sequence time runs top to bottom. Keep 40 px between shapes and 24 px of margin around the edge.
- **Text fits**: make each box at least 8 px wide per character of its longest line, plus 24 px. A box line holds at most 28 characters; break longer text into a second `tspan`.
- **Every arrow has a label**: a verb or the data it carries ("price-changed event", "POST /alerts"). Number the arrows (1, 2, 3) when order matters.
- **No crossings** where a different layout avoids them. Route around a box, never through it.
- **Legend** at the bottom, for every shape style the diagram uses.
- **Labels never sit on a line**: put each label in open space, for example between two lifelines. When a label must cross a line, put a white `rect` behind it. A white outline on the text (`stroke="#FFFFFF" stroke-width="4" paint-order="stroke"`) only helps where a line touches the letters, not where it passes between them.
- **Escape** `&` as `&amp;` and `<` as `&lt;` inside text.
- **Look at it** before finishing. Open the file in a browser or image viewer (in Claude Code, the browser pane), and fix clipped text, overlaps, and stray arrows. At the least, check that it parses: `python3 -c "import sys,xml.dom.minidom as m; m.parse(sys.argv[1])" <file>`.

## Palette
| Shape | Fill | Stroke |
|---|---|---|
| Our component | `#EEF2FF` | `#4F46E5` |
| Data store (cylinder) | `#ECFDF5` | `#059669` |
| Queue or stream | `#FFF7ED` | `#EA580C` |
| External or other team | `#F3F4F6` | `#6B7280`, dashed |
| Client app | `#FDF2F8` | `#DB2777` |

Text `#111827`; arrows and labels `#374151`. Font `font-family="-apple-system, Segoe UI, Helvetica, Arial, sans-serif"`: 14 px in shapes, 12 px on arrows, 18 px bold for the title.

## Shared header
Every file starts like this. Change the size and the title.

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="960" height="420" viewBox="0 0 960 420"
     font-family="-apple-system, Segoe UI, Helvetica, Arial, sans-serif" font-size="14" fill="#111827">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="8" markerHeight="8" orient="auto-start-reverse">
      <path d="M0,0 L10,5 L0,10 z" fill="#374151"/>
    </marker>
  </defs>
  <rect width="100%" height="100%" fill="#FFFFFF"/>
  <text x="24" y="36" font-size="18" font-weight="700">Price alerts: architecture</text>
  <!-- shapes -->
</svg>
```

## Architecture shapes
```svg
<!-- our component: rect + centered text -->
<rect x="200" y="80" width="180" height="64" rx="8" fill="#EEF2FF" stroke="#4F46E5" stroke-width="1.5"/>
<text x="290" y="108" text-anchor="middle" font-weight="600">Alert service</text>
<text x="290" y="128" text-anchor="middle" font-size="12" fill="#374151">matches drops to savers</text>

<!-- external or other team: dashed -->
<rect x="700" y="80" width="180" height="64" rx="8" fill="#F3F4F6" stroke="#6B7280" stroke-width="1.5" stroke-dasharray="6 4"/>

<!-- data store: cylinder 160 wide; the top ellipse starts at (420, 250), the body is 60 tall -->
<path d="M420,250 a80,12 0 0,0 160,0 a80,12 0 0,0 -160,0 v60 a80,12 0 0,0 160,0 v-60"
      fill="#ECFDF5" stroke="#059669" stroke-width="1.5"/>
<text x="500" y="295" text-anchor="middle" font-weight="600">Saved items DB</text>

<!-- queue: rect with stream bars -->
<rect x="40" y="80" width="120" height="64" rx="8" fill="#FFF7ED" stroke="#EA580C" stroke-width="1.5"/>
<path d="M60,96 v32 M72,96 v32 M84,96 v32" stroke="#EA580C" stroke-width="1.5"/>

<!-- labeled arrow: line + label with a white backing -->
<line x1="380" y1="112" x2="696" y2="112" stroke="#374151" stroke-width="1.5" marker-end="url(#arrow)"/>
<rect x="470" y="96" width="136" height="18" fill="#FFFFFF"/>
<text x="538" y="109" text-anchor="middle" font-size="12" fill="#374151">2. send(userId, msg)</text>

<!-- bent arrow: polyline -->
<polyline points="290,144 290,200 500,200 500,236" fill="none" stroke="#374151" stroke-width="1.5" marker-end="url(#arrow)"/>
```

## Class diagram shapes
One box per entity: a header with the name, then one line per field (`name: type`), with `PK` and `FK` marked. Relations are lines between boxes, with the cardinality (`1`, `0..1`, `*`) written at each end.

```svg
<!-- entity box: header band + field lines -->
<rect x="40" y="80" width="260" height="150" rx="6" fill="#FFFFFF" stroke="#4F46E5" stroke-width="1.5"/>
<path d="M40,86 a6,6 0 0,1 6,-6 h248 a6,6 0 0,1 6,6 v26 h-260 z" fill="#EEF2FF" stroke="#4F46E5" stroke-width="1.5"/>
<text x="170" y="102" text-anchor="middle" font-weight="700">SavedItem</text>
<g font-family="SFMono-Regular, Menlo, Consolas, monospace" font-size="12">
  <text x="52" y="134">PK id: uuid</text>
  <text x="52" y="154">FK user_id: uuid</text>
  <text x="52" y="174">FK product_id: string</text>
  <text x="52" y="194">saved_price: money</text>
  <text x="52" y="214">last_alert_price: money?</text>
</g>

<!-- relation: line + cardinality at each end -->
<line x1="300" y1="150" x2="440" y2="150" stroke="#374151" stroke-width="1.5"/>
<text x="308" y="142" font-size="12" fill="#374151">*</text>
<text x="424" y="142" font-size="12" fill="#374151">1</text>
<text x="370" y="168" text-anchor="middle" font-size="12" fill="#374151">saves</text>
```

## Sequence diagram shapes
Participants are boxes along the top, with dashed lifelines going down. Messages are horizontal arrows, numbered, top to bottom; a reply is dashed. An `alt` or `loop` frame is a rectangle around its messages, with the condition in its corner.

```svg
<!-- participant + lifeline -->
<rect x="40" y="60" width="160" height="40" rx="6" fill="#EEF2FF" stroke="#4F46E5" stroke-width="1.5"/>
<text x="120" y="85" text-anchor="middle" font-weight="600">Alert service</text>
<line x1="120" y1="100" x2="120" y2="520" stroke="#9CA3AF" stroke-dasharray="4 4"/>

<!-- message, then a dashed reply -->
<line x1="120" y1="140" x2="396" y2="140" stroke="#374151" stroke-width="1.5" marker-end="url(#arrow)"/>
<text x="258" y="132" text-anchor="middle" font-size="12" fill="#374151">1. find savers(productId)</text>
<line x1="400" y1="176" x2="124" y2="176" stroke="#374151" stroke-width="1.5" stroke-dasharray="6 4" marker-end="url(#arrow)"/>
<text x="258" y="168" text-anchor="middle" font-size="12" fill="#374151">saved items</text>

<!-- alt frame -->
<rect x="24" y="200" width="560" height="120" fill="none" stroke="#6B7280"/>
<path d="M24,200 h90 v18 l-8,8 h-82 z" fill="#F3F4F6" stroke="#6B7280"/>
<text x="32" y="216" font-size="12" font-weight="700">alt</text>
<text x="124" y="216" font-size="12" fill="#374151">[price still below threshold]</text>
```
