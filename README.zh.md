# Playwright Screenshot — 高 DPI HTML 截图

> `npx skills add veryos/playwright-screenshot`

用 Playwright **DPR=5** 替代 wkhtmltoimage。390px 移动端卡片，每个汉字从 13 像素跃升到 65 像素渲染——4 倍超采样，字体锐利清澈。

![DPR对比](assets/dpr-comparison.png)

*左：DPR=1（125KB）。右：DPR=5（716KB，4 倍超采样）。同一 HTML，同一 390px 宽度，同一微软雅黑字体。*

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

| DPR | 390px 渲染宽度 | 文件 | 场景 |
|-----|-------------|------|------|
| 1 | 390px | 125KB | 调试对比 |
| 2 | 780px | 270KB | 快速预览 |
| 3 | 1170px | 420KB | 日常 |
| **5** | **1950px** | **700KB** | **正式发布（默认）** |

## 从 wkhtmltoimage 迁移

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `viewport=390` + `quality=95` |
| QtWebKit 引擎 | Chromium Skia |
| ❌ 无 DPR | ✅ `device_scale_factor=5` |

## 中文推荐字体

```css
body { font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif; }
h1   { font-family: 'SimHei', sans-serif; }
```

## License

MIT
