# Leds system Matrix Builder & Editor

The **Leds system Matrix Builder & Editor** (`matrix_builder.html`) is a browser-based utility for designing and editing coordinate-to-LED index maps. It is essential when designing custom led displays, non-linear pixel geometries (like snake patterns or circles), or matrices with irregular layouts (gaps/empty spaces).

---

## The Concept of a Matrix Map

When rendering 2D text or effects, code addresses pixels using `(x, y)` Cartesian coordinates. However, addressable LED strips (like WS2812B/NeoPixels) are wired sequentially: LED `0`, LED `1`, LED `2`, etc.

The matrix map bridges this gap:
- It is a 2D grid matching your display dimensions.
- Each cell contains the physical LED index corresponding to that coordinate.
- Cells that do not contain an LED (empty gaps) are assigned a value of `-1`.

---

## Getting Started

1. Navigate to the `tools` directory in your file explorer.
2. Open `matrix_builder.html` in any web browser.
3. The editor runs completely locally and stores your progress automatically in your browser's local storage.

---

## How to Edit

### 1. Configure Dimensions
In the top settings bar, set the **Width** and **Height** of your display grid, then click **Set Grid**.

### 2. Cell Selection & Hotkeys
- **Single Cell**: Click in any cell and type an integer index (or `-1` for empty).
- **Multi-Select**: Hold down your mouse and drag across multiple cells to select them.
- **Bulk Actions (Toolbar)**:
  - **Invert Selection**: Selects all currently unselected cells.
  - **Clear Selection**: Deselects all cells.
  - **Clear Selected Values**: Resets all selected cells to `-1`.
  - **Auto-Fill Selection**: Sequential index fill (prompts you for a starting index, then fills selected cells left-to-right, top-to-bottom).
  - **Add Offset**: Prompts for a number (positive or negative) to add to the value of all selected cells.
- **Undo / Redo**: Quick undo is supported with `Ctrl+Z` (up to 50 states of history).

### 3. Template Tools (18x14 Matrix Only)
When the grid is configured to **18 columns** by **14 rows**, two special template generator buttons appear:
- **Load Symmetrical Template**: Generates a pre-configured layout suitable for symmetric dual-panel builds.
- **Load Mirrored Template**: Generates a mirrored pattern suitable for reverse-wired or mirrored LED panels.

---

## Validation Engine

To prevent bugs on your ESP32, the builder runs real-time checks on the active matrix:
1. **Duplicate Check**: Checks if any LED index (excluding `-1`) is used more than once.
2. **Missing Check**: Determines the maximum index and checks if any intermediate integers are missing.
3. **Status Pill**: Shows a color-coded status badge:
   - `All Valid` (Green): Matrix has no gaps or duplicate indices.
   - `Has Duplicates` (Red): Same physical LED mapped to multiple coordinates.
   - `Has Missing` (Orange): Non-sequential wiring or unmapped physical pixels.

---

## Exporting & Importing

The right panel displays generated code in real time:

### 1. Python Code Representation
Generates a nested Python tuple. Copy this structure directly into your project (e.g. into `settings.json` or config scripts):

```python
LED1_MATRIX = (
    (-1, -1,  0,  1,  2, -1, -1),
    (-1,  3,  4,  5,  6,  7, -1),
    ( 8,  9, 10, 11, 12, 13, 14),
)
```

### 2. Hex Representation
Generates a compact hexadecimal string (2 characters per cell, with `-1` encoded as `ff`):
```text
ffff000102fffffff0304050607ff08090a0b0c0d0e
```

### 3. Importing Existing Maps
To edit an existing layout:
1. Paste the Python tuple representation (e.g. `LED1_MATRIX = ((...))`) into the **Import Python Tuple** box.
2. Click **Import**.
3. The editor automatically adjusts to the correct width/height and populates the grid cells.
