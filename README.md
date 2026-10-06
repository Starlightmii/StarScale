# StarScale

**AI & GPU image upscaler — one HTML file, no install, no server.**

Open `index.html` in any modern browser (Chrome / Edge / Firefox, desktop or Android) and start upscaling. Everything — UI, WebGL2 shaders, AI worker — lives in this single file.

```
AI (Real-ESRGAN family, ONNX)  ·  GPU (WebGL2 Lanczos/Catmull-Rom/B-spline)
Batch queue  ·  Color grading  ·  Crop  ·  Face recovery  ·  Compare modes
Export PNG/WebP/JPEG or ZIP  ·  Works from file://
```

## Quick start

**Desktop:** double-click `index.html`. Done.

**Android:** open the file in Chrome (e.g. via your Files app → open with Chrome), or serve it:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

Optional — add it as an app: Chrome menu → *Add to Home screen*.

## Two engines

| | GPU Resample | AI Neural |
|---|---|---|
| Tech | WebGL2, separable kernels | ONNX Runtime Web in a Web Worker |
| Models | Lanczos3 · Catmull-Rom · B-spline · nearest + edge-aware detail pass | Real-ESRGAN v3 · PurePhoto SPAN · ClearReality · Rybu (anime) · SPAN 2× · RealPLKSR HQ · DeJPEG pre-pass |
| Speed | Instant, any size | Tiled, minutes for large images |
| Download | Nothing | Model (~1.7–29 MB) fetched once from Hugging Face, cached in IndexedDB |

The app **analyzes each import** (subject type, noise, blur, JPEG artefacts, exposure) and recommends an engine/model — you can always override.

GPU resample runs fully offline. AI models download from Hugging Face on first use and are then cached; there is also a *Custom .onnx* picker for any ONNX super-resolution model on your device.

## Features

- **Import analysis + recommendations** — rule-based, transparent, one-tap apply
- **Presets** — quick quality/speed trade-offs
- **Output & memory estimator** before you commit
- **Color grading** (GLSL) — neutral by default, nothing is changed without you asking
- **Crop** before upscaling
- **Face detection + recovery** (UltraFace ONNX) for portrait work
- **Compare modes** — split, side-by-side, difference, flicker
- **Batch queue** with drag-reorder, per-item engine/model override
- **Export** — PNG / JPEG / WebP, EXIF preserved on JPEG, or all-at-once ZIP
- **Share** via the native share sheet on Android
- **History** of recent runs
- **Command palette** (Ctrl/Cmd-K) · light "paper" and dark "pro" themes

## Why it works from `file://`

Most single-file AI demos break when opened directly from disk because browsers block workers and cross-origin scripts on opaque origins. StarScale takes a measured path: it fetches the ONNX Runtime source over HTTP (allowed from `file://`), builds a Blob worker from an inlined worker source string, and injects the runtime via `importScripts`. No server, no bundler. (Verified in Chrome 151; Firefox also works.)

## Privacy

- Images never leave your device. Inference runs locally (WebGL / WebGPU / WASM).
- The only network requests are: Google Fonts (visual theme, degrades gracefully offline), ONNX Runtime script (jsDelivr/unpkg), and AI model downloads (Hugging Face) — all fetched once and cached.
- IndexedDB/localStorage keys are namespaced `starscale.*`.

## Security notes / threat model

Built as a **local, single-user tool**. Its `innerHTML` uses interpolate only app-internal constants (scale values, built-in palette labels) — no filenames or user-derived text. Don't host it on a shared origin and don't feed it untrusted model files without understanding them.

## Sources & credits

| Component | Source |
|---|---|
| ONNX Runtime Web 1.20.0 | [microsoft/onnxruntime](https://github.com/microsoft/onnxruntime) via [jsDelivr](https://cdn.jsdelivr.net/npm/onnxruntime-web@1.20.0/dist/) / [unpkg](https://unpkg.com/onnxruntime-web@1.20.0/dist/) mirrors |
| Real-ESRGAN v3 (realesr-general-x4v3, ONNX) | [CoderViking/realesr-general-x4v3-onnx](https://huggingface.co/CoderViking/realesr-general-x4v3-onnx) |
| PurePhoto SPAN / ClearReality / Rybu / SPAN 2× / DeJPEG / RealPLKSR (ONNX) | [huggingworld/onnx-image-models](https://huggingface.co/huggingworld/onnx-image-models) |
| UltraFace RFB-320 (face detect, ONNX) | [onnxmodelzoo/version-RFB-320](https://huggingface.co/onnxmodelzoo/version-RFB-320) |
| Real-ESRGAN (original research) | [xinntao/Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) · paper: *Real-ESRGAN: Training Real-World Blind Super-Resolution with Pure Synthetic Data* (Li et al., 2021) |
| SPAN architecture | [chenghao-zhuo/span](https://github.com/chenghao-zhuo/span) |
| IM Fell English, Cardo, Special Elite fonts | [Google Fonts](https://fonts.google.com/) |

Model weights keep their respective licenses — check each Hugging Face page before commercial use.

## Repository layout

```
index.html   — the entire app (UI, GLSL, worker, ~4.6k lines)
README.md
LICENSE
```

## License

MIT — see [LICENSE](LICENSE).
