# Image Conversion Tracker

Visual workflow editor for managing AI-driven image transformations in the browser. The app is built with vanilla JavaScript, Tailwind CSS, and vis-network; no bundler or framework is required.

## What the system does
- **Node-based workflow editing**: create/delete nodes, connect edges, inspect node/edge detail panels, and auto-layout the graph for readability.
- **Prompt editing with aspect ratio support**: edges store prompts, models, and aspect ratios that are editable via the prompt modal. Aspect ratio values are rendered in the edge detail panel (e.g., `1:1`, `4:3`, `16:9`).
- **Batch generation controls**: UI buttons for batch generate, upload-to-node, merge, and transform operations are wired through `workflowApp.js` and `canvasController.js` to update the network state.
- **Configurable API access**: users can enter Replicate API keys (and IO Intelligence API keys) in the settings modal; keys are saved to `localStorage` via `Config`.
- **Replicate/Nano Banana integration**: `TransformationService` calls the Cloudflare Worker proxy for `google/nano-banana` predictions, honoring aspect ratio and output format settings.

## System components
- **Front-end**: `index.html`, `css/workflow.css`, and JS modules under `js/` (`workflowApp.js`, `canvasController.js`, `config.js`, `transformationService.js`, etc.).
- **Cloudflare Worker proxy**: `cloudflare-worker/cloudflare-worker-v5.js` implements the Replicate API proxy with optional R2 persistence and validation against allowed Replicate endpoints.
- **Shell helper**: `sh/imgen_batch.sh` offers a CLI wrapper for batch Replicate calls.

## Running the app
1. Serve the repository root with any static server (e.g., `python -m http.server 8000`) and open `index.html` in a browser.
2. Open the settings modal (⚙️) and enter a Replicate API key; the default proxy endpoint is `https://replicate-nanobanana.skume-bioinfo.workers.dev/proxy`.
3. Add nodes, connect edges, set prompts/aspect ratios, and trigger transformations or batch generation from the toolbar.

## Implementation checks (as of this revision)
- ✅ Aspect ratio fields and prompt editing UIs are present in `index.html`/`workflowApp.js` and stored on edges.
- ✅ Replicate API calls are routed through the Cloudflare Worker proxy in `TransformationService`.
- ⚠️ A favicon is referenced at `../favicon/nu-circle.svg`, but no `favicon/` directory is present in the repository.
- ⚠️ Cloudflare Worker deployment is not automated; you must deploy `cloudflare-worker-v5.js` yourself and supply the endpoint.
