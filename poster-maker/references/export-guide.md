# Export Guide — Rendering & Output

This guide covers all methods for turning a poster HTML template into a high-quality image or print-ready file.

---

## Resolution Strategy

The poster HTML canvas is sized in **CSS pixels**. The rendered PNG depends on:

```
Output PNG pixels = CSS canvas px × devicePixelRatio (DPR)
```

| Canvas (CSS px) | DPR | Output PNG | Effective DPI @ A1 print |
|-----------------|-----|------------|--------------------------|
| 1748×2480 | 1 | 1748×2480 | 75 DPI — screen only |
| 1748×2480 | 2 | 3496×4960 | 150 DPI — conference print OK |
| 1748×2480 | 3 | 5244×7440 | 225 DPI — professional print |
| 2480×3508 (A0) | 2 | 4960×7016 | 150 DPI @ A0 |

**Recommendation:** DPR 2 for conference posters, DPR 3 for professional print. PDF is always better for print (vector, any DPI).

---

## Method 1: Playwright MCP (Primary — PNG Output)

Use Playwright MCP tools already available in Claude Code.

**Important:** Playwright blocks `file://` protocol. Always serve the poster via a local HTTP server first:

```bash
# Run in ./output/ directory (or wherever the HTML file is)
python -m http.server 8743
# Then use: http://localhost:8743/poster-name.html
```

### Standard screenshot (DPR 1)

```
1. Start HTTP server in output/ directory (see above)

2. mcp__playwright → browser_navigate
   url: "http://localhost:8743/poster-name.html"

3. mcp__playwright → browser_resize
   width: 1748   (A1 portrait width)
   height: 2480  (A1 portrait height)

4. mcp__playwright → browser_wait_for
   time: 2       (wait for Google Fonts to load)

5. mcp__playwright → browser_take_screenshot
   fullPage: false
   type: "png"
   filename: "./output/poster-name.png"
```

### High-DPR screenshot (DPR 2 — for print quality)

Use `browser_run_code_unsafe` for DPR control:

```javascript
async (page) => {
  const browser = page.context().browser();
  const ctx = await browser.newContext({ deviceScaleFactor: 2 });
  const p2 = await ctx.newPage();
  await p2.setViewportSize({ width: 1748, height: 2480 });
  await p2.goto('http://localhost:8743/poster-name.html');
  await p2.waitForLoadState('networkidle'); // wait for fonts
  await p2.screenshot({ path: './output/poster-name-2x.png' });
  await ctx.close();
  return 'done';
}
```

### Canvas size reference by poster size

| Size | Portrait (W×H) | Landscape (W×H) |
|------|----------------|-----------------|
| A2 | 1240×1754 | 1754×1240 |
| **A1** | **1748×2480** | **2480×1748** |
| A0 | 2480×3508 | 3508×2480 |
| 18×24in | 1728×2304 | 2304×1728 |
| 36×48in | 3456×4608 | 4608×3456 |

---

## Method 2: Chrome Print to PDF (Best for Print — Vector)

This is the highest quality output. Instruct user after generating HTML:

> **To get print-quality PDF:**
> 1. Open `poster-name.html` in Chrome or Edge
> 2. Press `Ctrl+P` (Windows) / `Cmd+P` (Mac)
> 3. Change "Destination" to **"Save as PDF"**
> 4. Click **"More settings"**
> 5. Paper size: **A1** (or A0/A2 as needed)
> 6. Margins: **None**
> 7. Scale: **100%** (not "Fit to page")
> 8. Background graphics: ✓ **Enabled**
> 9. Click Save
>
> This outputs a vector PDF at exactly A1 size. Any print shop can print from this.

**The HTML template already includes:**
```css
@page { size: A1 portrait; margin: 0; }
@media print {
  html, body { width: 594mm; height: 841mm; overflow: hidden; }
  body { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
  .poster {
    position: fixed !important; top: 0 !important; left: 0 !important;
    width: 1748px !important; height: 2480px !important;
    transform: scale(1.2843) !important; transform-origin: 0 0 !important;
    overflow: hidden !important;
  }
}
/* Scale math: 594mm / (1748px/96dpi × 25.4) = 1.2843 — exact A1 fit */
```

This `transform: scale` approach prevents the previous bug where `height: 100vh` expanded the canvas from 2480px to ~3178px, causing layout shifts and misaligned text wrapping in print.

---

## Method 3: Playwright PDF (Programmatic Vector PDF)

Playwright can also generate PDFs directly. Use `browser_run_code_unsafe`:

```javascript
async (page) => {
  // Requires HTTP server: python -m http.server 8743 (in output/ dir)
  await page.goto('http://localhost:8743/poster-name.html');
  await page.waitForLoadState('networkidle'); // font loading
  await page.pdf({
    path: './output/poster-name.pdf',
    width: '594mm',   // A1 portrait
    height: '841mm',
    printBackground: true,
    margin: { top: '0', right: '0', bottom: '0', left: '0' }
  });
  return 'PDF saved';
}
```

For landscape A1: `width: '841mm', height: '594mm'`.
For A0: `width: '841mm', height: '1189mm'`.

---

## Method 4: Fallback — Chrome DevTools MCP Screenshot

If Playwright is not working, use Chrome DevTools MCP:

```
1. Start HTTP server: python -m http.server 8743 (in output/ dir)

2. mcp__chrome-devtools → navigate_page
   type: "url"
   url: "http://localhost:8743/poster-name.html"

2. mcp__chrome-devtools → resize_page
   width: 1748
   height: 2480

3. mcp__chrome-devtools → take_screenshot
   fullPage: false
   format: "png"
   filePath: "./output/poster-name.png"
```

---

## Font Loading Wait

Always wait for fonts before screenshotting. Google Fonts may take 500–1500ms.

In Playwright code: `await page.waitForTimeout(1500);`
Or wait for network idle: `await page.waitForLoadState('networkidle');`

If fonts fail to load (no internet / CDN blocked):
- The HTML will fall back to system sans-serif (acceptable, not ideal)
- Mention this in output: "If fonts appear incorrect, open the HTML in Chrome first while connected to the internet"

---

## Output File Structure

```
output/
├── poster-{slug}-{YYYYMMDD}.html    ← self-contained, editable
├── poster-{slug}-{YYYYMMDD}.png     ← screen preview (DPR 1)
└── poster-{slug}-{YYYYMMDD}-2x.png  ← print PNG (DPR 2, optional)
```

`slug` = first 4 meaningful words of title, kebab-cased, lowercase ASCII only.
Example: "Deep Learning Reduces Cancer Detection Errors by 43%" → `deep-learning-reduces-cancer-20250523.html`

---

## Quality Checklist Before Delivering

Run these checks after screenshot:

- [ ] Screenshot file is not 0 bytes
- [ ] Background is not pure white (if dark theme)
- [ ] Header is fully visible (title not cut off)
- [ ] Footer is visible (at least 1 reference + QR or URL)
- [ ] All body columns are present and non-empty
- [ ] No `{{PLACEHOLDER}}` text visible in screenshot
- [ ] No obvious overflow (content cut at edges)
- [ ] Font is rendering (not fallback monospace everywhere)

If any fails: open HTML in browser, identify the issue, edit HTML, re-screenshot.

---

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| White background | Dark theme CSS not loaded | Check `<body>` has no `class` attribute override; check `--bg` var defined |
| Font is monospace everywhere | Google Fonts CDN not reachable | Add `await page.waitForLoadState('networkidle')` |
| Screenshot is blank | Page not fully rendered | Add `await page.waitForTimeout(2000)` |
| Content overflow visible | Too much text | Reduce body text in HTML, rerun |
| Page size wrong | Viewport not set | `browser_resize(1748, 2480)` before screenshot |
| PDF margins not zero | Print settings | Ensure `@page { margin: 0 }` in CSS |
