# Playwright Screenshot — High-DPI HTML → PNG

> `npx skills add veryos/playwright-screenshot`

Replace wkhtmltoimage with Playwright at **deviceScaleFactor=5** for crystal-clear Chinese text. Born from real-world testing across 390px mobile cards and 1080px daily reports — DPR=5 renders at 1950px then scales down for 4× oversampling.

## Quick Start

```bash
npx skills add veryos/playwright-screenshot
pip install playwright pillow
python -m playwright install chromium
```

## DPR Comparison

| DPR | Render width (390px) | File | Clarity |
|-----|---------------------|------|---------|
| 1 | 390px | 125KB | wkhtmltoimage level |
| 2 | 780px | 270KB | noticeable gain |
| 3 | 1170px | 420KB | good |
| **5** | **1950px** | **700KB** | **sweet spot — near 2K density** |

## Migration from wkhtmltoimage

| wkhtmltoimage | Playwright |
|---|---|
| `--width 390 --quality 95` | `device_scale_factor=5 viewport=390` |
| QtWebKit engine | Chromium Skia |
| No DPR support | DPR=5 ✨ |

## Font Recommendation

```css
body { font-family: 'Microsoft YaHei', sans-serif; }
h1   { font-family: 'SimHei', sans-serif; }
```

## License

MIT
