# Fusion 360 MCP Server - Available Tools

The Fusion 360 MCP server provides **10 tools** for creating 3D models via Claude:

## 📐 Sketch Tools

### 1. **CreateSketch**
Creates a new sketch on a specified plane.

**Parameters:**
- `plane` (string, required) - The plane to create the sketch on: 'xy', 'yz', or 'xz'

**Example:** "Create a sketch on the XY plane"

---

### 2. **DrawRectangle**
Draws a rectangle in the active sketch.

**Parameters:**
- `width` (number, required) - Width of the rectangle in mm
- `depth` (number, required) - Depth of the rectangle in mm
- `origin_x` (number, optional) - X coordinate of the origin point (default: 0)
- `origin_y` (number, optional) - Y coordinate of the origin point (default: 0)
- `origin_z` (number, optional) - Z coordinate of the origin point (default: 0)

**Example:** "Draw a 50mm x 30mm rectangle"

---

### 3. **DrawCircle**
Draws a circle in the active sketch.

**Parameters:**
- `radius` (number, required) - Radius of the circle in mm
- `center_x` (number, optional) - X coordinate of the center point (default: 0)
- `center_y` (number, optional) - Y coordinate of the center point (default: 0)
- `center_z` (number, optional) - Z coordinate of the center point (default: 0)

**Example:** "Draw a circle with 25mm radius"

---

## 🔧 3D Creation Tools

### 4. **Extrude**
Extrudes a profile into a 3D body.

**Parameters:**
- `height` (number, required) - Height of the extrusion in mm
- `profile_index` (integer, optional) - Index of the profile to extrude (default: 0)
- `operation` (string, optional) - Operation type: 'new', 'join', 'cut', 'intersect' (default: 'new')

**Example:** "Extrude the profile 20mm"

---

### 5. **Revolve**
Revolves a profile around an axis.

**Parameters:**
- `axis_origin_x` (number, required) - X coordinate of the axis origin
- `axis_origin_y` (number, required) - Y coordinate of the axis origin
- `axis_origin_z` (number, required) - Z coordinate of the axis origin
- `axis_direction_x` (number, required) - X component of the axis direction vector
- `axis_direction_y` (number, required) - Y component of the axis direction vector
- `axis_direction_z` (number, required) - Z component of the axis direction vector
- `profile_index` (integer, optional) - Index of the profile to revolve (default: 0)
- `angle` (number, optional) - Angle of revolution in degrees (default: 360)
- `operation` (string, optional) - Operation type: 'new', 'join', 'cut', 'intersect' (default: 'new')

**Example:** "Revolve the profile 360 degrees around the Y axis"

---

## ✨ Modification Tools

### 6. **Fillet**
Adds a fillet (rounded edge) to selected edges.

**Parameters:**
- `radius` (number, required) - Radius of the fillet in mm
- `body_index` (integer, optional) - Index of the body containing the edges (default: 0)
- `edge_indices` (array, optional) - Indices of specific edges to fillet. If empty, all edges are filleted (default: [])

**Example:** "Add 2mm fillets to all edges"

---

### 7. **Chamfer**
Adds a chamfer (beveled edge) to selected edges.

**Parameters:**
- `distance` (number, required) - Distance of the chamfer in mm
- `body_index` (integer, optional) - Index of the body containing the edges (default: 0)
- `edge_indices` (array, optional) - Indices of specific edges to chamfer. If empty, all edges are chamfered (default: [])

**Example:** "Add 1mm chamfers to all edges"

---

### 8. **Shell**
Hollows out a solid body with a specified wall thickness.

**Parameters:**
- `thickness` (number, required) - Thickness of the shell walls in mm
- `body_index` (integer, optional) - Index of the body to shell (default: 0)
- `face_indices` (array, optional) - Indices of faces to remove. If empty, no faces are removed (default: [])

**Example:** "Shell the body with 2mm wall thickness"

---

## 🔀 Boolean Operations

### 9. **Combine**
Combines two bodies using boolean operations.

**Parameters:**
- `operation` (string, optional) - Operation type: 'join', 'cut', 'intersect' (default: 'join')
- `target_body_index` (integer, optional) - Index of the target body (default: 0)
- `tool_body_index` (integer, optional) - Index of the tool body (default: 1)

**Example:** "Combine the two bodies using join operation"

---

## 💾 Export Tools

### 10. **ExportBody**
Exports a body to a file.

**Parameters:**
- `filename` (string, required) - The filename to export to
- `body_index` (integer, optional) - Index of the body to export (default: 0)
- `format` (string, optional) - Export format: 'stl', 'obj', 'step', 'iges', 'sat' (default: 'stl')

**Example:** "Export the body as 'mypart.stl'"

---

## 🎯 Example Workflows

### Create a Simple Box
```
1. CreateSketch (plane: 'xy')
2. DrawRectangle (width: 50, depth: 50)
3. Extrude (height: 30)
```

### Create a Box with Rounded Corners
```
1. CreateSketch (plane: 'xy')
2. DrawRectangle (width: 100, depth: 100)
3. Extrude (height: 50)
4. Fillet (radius: 5)
```

### Create a Cylinder
```
1. CreateSketch (plane: 'xy')
2. DrawCircle (radius: 25)
3. Extrude (height: 100)
```

### Create a Hollow Box
```
1. CreateSketch (plane: 'xy')
2. DrawRectangle (width: 60, depth: 60)
3. Extrude (height: 40)
4. Shell (thickness: 3, face_indices: [])  # Removes top face
```

---

## 📝 How to Use

### In Claude Desktop:

Ask Claude things like:
- "Create a 50mm cube in Fusion 360"
- "Make a cylinder 25mm radius and 100mm high"
- "Design a box 80x60x40mm with 3mm fillets on all edges"

Claude will use these tools to generate a Python script for Fusion 360.

### Running the Generated Script:

1. Copy the script Claude generates
2. Open Fusion 360
3. Press `Shift + S` (Scripts and Add-ins)
4. Click the green "+" button
5. Paste and run the script

---

## 🔍 Troubleshooting

If Claude can't see the tools:
1. Check Settings → Developer → Local MCP servers
2. Verify "fusion360" shows as "running" (not "failed")
3. **Start a NEW chat** - Claude only loads MCP tools at the start of a conversation
4. Ask: "What tools do you have from the fusion360 MCP server?"
