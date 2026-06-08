# Playwright Screenshot — 高 DPI HTML 截图

> `npx skills add veryos/playwright-screenshot`

用 Playwright **DPR=5** 替代 wkhtmltoimage。5 倍分辨率渲染后缩放——390px 移动端卡片，每个汉字从 13 像素跃升到 65 像素。本地效果肉眼可辨，但 GitHub README 会压缩图片导致看不出差异。装上自己跑一下就知道了。

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
    ctx = browser.new_context(
        device_scale_factor=5,       # ← 核心参数
        viewport={"width": 390, "height": 960}
    )
    page = ctx.new_page()
    page.goto("file://output.html")
    page.screenshot(path="output.png", full_page=True)
    ctx.close()
    browser.close()

img = Image.open("output.png")
img = img.resize(
    (390, int(img.height * 390 / img.width)), Image.LANCZOS
)
img.save("output.png", quality=95)
```

## 从 wkhtmltoimage 迁移

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `viewport=390` + `quality=95` |
| QtWebKit | Chromium Skia |
| ❌ 无 DPR | ✅ `device_scale_factor=5` |

## License

MIT
