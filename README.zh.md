# Playwright Screenshot — 高 DPI HTML 截图

> `npx skills add veryos/playwright-screenshot`

用 Playwright **DPR=5** 替代 wkhtmltoimage。

**DPR=1** · 128KB · wkhtmltoimage 级别

![](assets/dpr-1.png)

**DPR=5** · 221KB · 4 倍超采样

![](assets/dpr-5.png)

## 快速开始

```bash
npx skills add veryos/playwright-screenshot
pip install playwright pillow
python -m playwright install chromium
```

## License

MIT
