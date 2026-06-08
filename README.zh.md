# Playwright Screenshot — 高 DPI HTML 截图

> `npx skills add veryos/playwright-screenshot`

用 Playwright **DPR=5** 替代 wkhtmltoimage。同一 HTML，同一宽度——DPR=5 以 5 倍分辨率渲染后缩放，中文字体锐利不粘连。

**DPR=1** · wkhtmltoimage 级别 · 128KB

<img src="assets/dpr-1.png" width="100%" alt="DPR=1">

**DPR=5** · 4 倍超采样 · 221KB

<img src="assets/dpr-5.png" width="100%" alt="DPR=5">

## 快速开始

```bash
npx skills add veryos/playwright-screenshot
pip install playwright pillow
python -m playwright install chromium
```

## 使用

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

## DPR 选择

| DPR | 渲染宽度 | 文件 | 场景 |
|-----|---------|------|------|
| 1 | 390px | 125KB | 调试 |
| 2 | 780px | 270KB | 预览 |
| 3 | 1170px | 420KB | 日常 |
| **5** | **1950px** | **700KB** | **正式** |

## 从 wkhtmltoimage 迁移

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `viewport=390` + `quality=95` |
| QtWebKit | Chromium Skia |
| ❌ 无 DPR | ✅ `device_scale_factor=5` |

## 字体

```css
body { font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif; }
h1   { font-family: 'SimHei', sans-serif; }
```

## License

MIT
