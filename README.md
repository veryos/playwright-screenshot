# Playwright Screenshot — High-DPI HTML → PNG

> `npx skills add veryos/playwright-screenshot`

Replace wkhtmltoimage with Playwright at **deviceScaleFactor=5**. Same HTML, same 390px — DPR=5 renders at 1950px then scales down for sharp Chinese text.

![DPR=1](assets/dpr-1.png)

*DPR=1 — 125KB, wkhtmltoimage level*

![DPR=5](assets/dpr-5.png)

*DPR=5 — 716KB, 4× oversampling*

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
    ctx = browser.new_context(device_scale_factor=5, viewport={"width": 390, "height": 960})
    page = ctx.new_page()
    page.goto("file://output.html")
    page.screenshot(path="output.png", full_page=True)
    ctx.close()
    browser.close()

img = Image.open("output.png")
img = img.resize((390, int(img.height * 390 / img.width)), Image.LANCZOS)
img.save("output.png", quality=95)
```

## DPR Selection

| DPR | Render width | File | Use |
|-----|-------------|------|-----|
| 1 | 390px | 125KB | Debug |
| 2 | 780px | 270KB | Preview |
| 3 | 1170px | 420KB | Daily |
| **5** | **1950px** | **700KB** | **Production** |

## Migration from wkhtmltoimage

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `viewport=390` + `quality=95` |
| QtWebKit | Chromium Skia |
| ❌ No DPR | ✅ `device_scale_factor=5` |

## Fonts

```css
body { font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif; }
h1   { font-family: 'SimHei', sans-serif; }
```

## License

MIT
