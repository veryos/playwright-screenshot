# Playwright Screenshot — High-DPI HTML → PNG

> `npx skills add veryos/playwright-screenshot`

Replace wkhtmltoimage with Playwright at **deviceScaleFactor=5**.

<table><tr>
<td width="50%"><b>DPR=1</b> · 122KB</td>
<td width="50%"><b>DPR=5</b> · 212KB · 4× oversampling</td>
</tr><tr>
<td><img src="assets/dpr-1.png" width="100%"></td>
<td><img src="assets/dpr-5.png" width="100%"></td>
</tr></table>

390px mobile card, Microsoft YaHei. DPR=5 renders at 1950px → downscale → crisp.

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

## License

MIT
