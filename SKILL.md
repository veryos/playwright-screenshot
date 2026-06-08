---
name: playwright-screenshot
description: 通用高 DPI 截图技能——Playwright DPR=5 渲染，替代 wkhtmltoimage。默认设备像素比 5，输出超采样 PNG。适用：日报信息图、HTML 转 PNG、任何需要清晰截图的场景。
category: devops
---

# Playwright 高 DPI 截图

替代 wkhtmltoimage 的标准化截图方案。2026-06-08 经微软雅黑 + DPR=1→5 实测验证，DPR=5 为肉眼可辨的上限甜点。

## 为什么换

- wkhtmltoimage 不支持 deviceScaleFactor，390px 下每个汉字仅 ~13 像素
- Playwright 支持 DPR=2~5，渲染 1950px 后缩至 390px，4 倍超采样
- 微软雅黑 / SimHei 等黑体类在低像素下比衬线锐利

## 标准用法

```python
from playwright.sync_api import sync_playwright
from PIL import Image

def screenshot_html(html_path, output_png, width=390, dpr=5):
    """HTML → 高 DPI PNG"""
    with sync_playwright() as p:
        browser = p.chromium.launch()
        ctx = browser.new_context(
            device_scale_factor=dpr,
            viewport={"width": width, "height": 960}
        )
        page = ctx.new_page()
        page.goto(f"file://{html_path}")
        page.screenshot(path=output_png, full_page=True)
        # 验证
        actual_dpr = page.evaluate("window.devicePixelRatio")
        ctx.close()
        browser.close()

    # 缩放到目标宽度
    img = Image.open(output_png)
    target_height = int(img.height * width / img.width)
    img = img.resize((width, target_height), Image.LANCZOS)
    img.save(output_png, quality=95)
```

## DPR 选择

| DPR | 390px 渲染宽 | 文件大小 | 适用场景 |
|---|---|---|---|
| 2 | 780px | ~270KB | 快速预览 |
| 3 | 1170px | ~420KB | 日常使用 |
| **5** | **1950px** | **~700KB** | **正式发布（默认）** |
| 1 | 390px | ~125KB | 仅调试对比用 |

## wkhtmltoimage → Playwright 参数映射

```python
# wkhtmltoimage (deprecated)          → Playwright (current)
# --width 390                         → viewport={"width": 390}
# --height 1920                       → viewport={"height": 1920}
# --quality 95                        → img.save(..., quality=95)
# --encoding UTF-8                    → 无需设置 (Playwright 默认)
# --disable-smart-width               → 无需设置
# --no-images                         → 无需设置
# --javascript-delay 0                → 无需设置 (Playwright 等页面加载完)
# --enable-local-file-access          → 无需设置 (file:// 默认支持)
# 无 DPR 参数                         → device_scale_factor=5 (核心优势)
```

## 字体推荐

```css
@font-face { font-family: 'YaHei';  src: local('Microsoft YaHei'), local('微软雅黑'); }
@font-face { font-family: 'SimHei'; src: local('SimHei'), local('黑体'); }
body  { font-family: 'YaHei', 'PingFang SC', sans-serif; }
h1    { font-family: 'SimHei', 'YaHei', sans-serif; }
.mono { font-family: 'JetBrains Mono', 'Fira Code', monospace; }
```

## 踩坑

- **`device_scale_factor` 必须在 `browser.new_context()` 上设置，`new_page()` 没有这个参数**。写错位置会导致 DPR 始终为 1，截图模糊。2026-06-08 实际踩坑：两次对比都跑了 DPR=1，白测了。
- 截图后用 `page.evaluate("window.devicePixelRatio")` 验证 DPR 实际值——这是唯一的真值检查
- wkhtmltoimage 的 `--width` 决定 viewport；Playwright 用 `viewport` 参数
- 字体必须已安装在系统中，用 `local()` 引用而非 Google Fonts @import
- 飞书/微信都会二次压缩图片，DPR=5 的超采样信息量是保证压缩后仍然清晰的关键
