# Tomy AI Studio — Online / Privacy-first

## Architecture

This is a static website edition. Host the site on any HTTPS static host; the user's browser runs the image editor. No ComfyUI, Python bridge, local AI server, or API key is required for the current tools.

### Available in this build
- Filters, brightness, contrast, saturation, undo, export, resize, and basic sharpening run locally in Canvas.
- Background removal runs in the browser using `@imgly/background-removal` 1.7.0 with the quantized `isnet_quint8` model and CPU execution selected for broad compatibility. The browser downloads the code and ONNX/WASM model assets on first use. The image itself is not uploaded to the model host.
- Watermark-region cleanup is a simple local patch operation, not AI inpainting.

### Optional: text-prompt AI editing (sends the image)
The AI tab calls a free public Hugging Face Space (default `multimodalart/Qwen-Image-Edit-Fast`) through `@gradio/client`. **This sends the (downscaled) image to Hugging Face and the Space owner.** The UI shows a warning and requires a consent checkbox before every request. Notes:
- Free anonymous GPU quota is small; users can paste their own Hugging Face token (kept in memory only) for a larger quota. Never embed a token in the HTML.
- Public Spaces can be renamed, paused, or change their API at any time; the Space ID is editable in the UI and the code discovers the endpoint/parameters at runtime.
- `@gradio/client` is loaded from jsDelivr (`@1`); pin an exact version after testing.
- For a production-grade setup, run your own inference server and require consent per image.

### Not yet available
AI super-resolution (2x/4x).

## Test locally

```powershell
python -m http.server 8000
```

Open `http://localhost:8000`.

## Publish online

Upload `index.html`, `README.md`, and `PRIVACY.md` to a static HTTPS host. The zip has these files at its root. No backend is needed for the currently enabled tools.

## Privacy

See [PRIVACY.md](PRIVACY.md). Except for the optional AI-editing tab (which sends the image to Hugging Face after explicit consent), the app does not upload selected images. The background-removal library and model assets are downloaded from external CDN infrastructure when first used. Those CDN requests do not contain the selected image. For stricter network isolation, download and self-host the JS/WASM/model assets on the same site after reviewing licensing.

## License notice

`@imgly/background-removal` is licensed under AGPL. If you distribute or host this as a closed-source/commercial service, review AGPL obligations and obtain legal/licensing advice or a separate commercial license if needed. The library's README describes in-browser execution, CDN-hosted model assets, and its license: https://www.npmjs.com/package/@imgly/background-removal

## Current limitations

- Text-prompt edits depend on a free public Space: quality, speed, and availability are not guaranteed.
- AI 2x/4x super-resolution is not yet connected.
- The existing sharpening slider is a local filter, not a generative upscaler.
- Watermark-region cleanup is approximate and should only be used on content you own or are authorized to edit.
