# Playwright Screenshot — High-DPI HTML → PNG

> `npx skills add veryos/playwright-screenshot`

Replace wkhtmltoimage with Playwright at **deviceScaleFactor=5**. Renders at 5× resolution then downscales — Chinese text goes from ~13 to ~65 physical pixels per character. The difference is obvious locally but invisible in a GitHub README (GitHub compresses images). Install it and see for yourself.

## Quick Start

```bash
npx skills add veryos/playwright-screenshot
pip install playwright pillow
python -m playwright install chromium
```

## Usage

```python
from playwright.sync_api import sync_playwright
from PIL import Image

with sync_playwright() as p:
    browser = p.chromium.launch()
    ctx = browser.new_context(
        device_scale_factor=5,       # ← the magic
        viewport={"width": 390, "height": 960}
    )
    page = ctx.new_page()
    page.goto("file://output.html")
    page.screenshot(path="output.png", full_page=True)
    ctx.close()
    browser.close()

# Downscale to target width
img = Image.open("output.png")
img = img.resize(
    (390, int(img.height * 390 / img.width)), Image.LANCZOS
)
img.save("output.png", quality=95)
```

## Why DPR=5

wkhtmltoimage has no deviceScaleFactor. On a 390px mobile card, each Chinese character gets ~13 physical pixels — strokes collapse and blur. DPR=5 renders internally at 1950px, giving each character ~65 pixels before downscaling. The result is visibly sharper on any platform that doesn't recompress images (Feishu, Telegram, local files).

## Migration from wkhtmltoimage

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `viewport=390` + `quality=95` |
| QtWebKit | Chromium Skia |
| ❌ No DPR | ✅ `device_scale_factor=5` |

## License

MIT
