# Timeline Graph Application Documentation

## Project Overview
The Timeline Graph application is a standalone, single-file HTML deliverable that visualizes machine breakdown events chronologically on an interactive HTML5 canvas. Designed for process engineers and manufacturing plant operators, the tool instantly bridges the gap between raw Excel logs and visual, shopfloor event timelines without requiring database backend integration or server installation. 

By analyzing the "Impact" textual descriptions from Excel rows, it contextually color-codes incident markers—instantly highlighting severe occurrences in red while keeping routine, unproblematic entries in blue. It also introduces automatic date and time detection algorithms, circumventing manual data cleanups.

## Technical Architecture
- **Environment**: Client-side execution exclusively (No backend, no authentication).
- **Core Technology**: 
  - **HTML5 Canvas**: Powers the robust geometric drawing of timeline lines, dynamic grids, event markers (`●`), context connectors, and wrapped text cards.
  - **Vanilla JavaScript**: Manages the application lifecycle, from hooking file-upload triggers to debounce-optimized re-rendering during window resize operations.
  - **CSS3**: Uses CSS Custom Properties (Variables) natively included via `<style>` to ensure portability while keeping a clean aesthetic system (White background, standardized margins).
- **External Dependencies**:
  - `SheetJS` (via CDN): Used strictly for traversing and parsing `.xlsx`/`.xls` binary sheets.

## Logic & Formulas
- **Dynamic Date & Time Normalization**:
  The application utilizes heuristic regex checks to decode dates coming from isolated systems (e.g., EU `DD/MM/YYYY` vs US `MM/DD/YYYY`). It aggressively scans all dates in a column for day values > 12 to resolve logic ambiguities and correctly normalize the records chronologically using `sort((a,b) => a.timestamp - b.timestamp)`.
  Fractional fractional-day times from Excel formats are also mathematically resolved into `HH:MM` syntax using standard minutes conversions: `Math.floor(totalMins / 60)`.
- **Negative Text Detection**:
  The application evaluates the `Impact` column via an inclusion scan array: `['scrap', 'loss', 'broken', 'fail', 'delay', 'downtime']` to flip its fillStyle payload conditionals.
- **Strict Canvas Left-Aligned Grid System**: 
  Horizontal spacing ensures cards never overlap:
  ```javascript
  const maxItemsForSpacing = Math.min(events.length, MAX_EVENTS_PER_LINE); // Default Max 6 horizontally
  const globalSegmentWidth = maxItemsForSpacing > 1 ? drawableWidth / (maxItemsForSpacing - 1) : 0;
  const currentX = sidePadding + (j * globalSegmentWidth); // Maps sequential items to discrete graphic columns extending from left to right.
  ```

## Feature Guide
1. **Dynamic File Uploading**: Matches headers by name (case-insensitive) rather than column position. It demands specific columns (`Date`, `Time`, `Event`, `Impact`, `By Whom`) while ignoring additional junk fields.
2. **"Load Demo Data" Mode**: A pre-seeded mock dataset instantly showcases varied error markers and unparsed time fallbacks. 
3. **Smart Row Skip & Warnings**: Erroneous row blocks trigger warning alerts with exact row counts printed at the top.
4. **Auto-Resize Canvas**: Dynamically alters resolution utilizing a 300ms debounce buffer against the resize event loop.
5. **Export Engine**: Implements the native Canvas API `toBlob()` mapping allowing one-click capture downloads of the chart as a `.png` format.

## User Instructions
1. Download or move the `timeline-graph.html` document precisely as provided.
2. Double-click the file to launch it in any modern browser (Google Chrome, Edge, or Firefox).
3. Click **Upload Excel File** to provide an `.xlsx` list of breakdown events. The column order is irrelevant, but the headers must exist.
4. Scan the top of the interface for missing times or unreadable Excel dates (presented as warnings).
5. The visual graphic is fully responsive; maximize your window or hit **Export to PNG** to secure an image copy directly onto your computer labeled automatically by the export's exact timestamp.
