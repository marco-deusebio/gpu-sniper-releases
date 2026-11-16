# GPU Sniper for Runpod

Grabs a scarce GPU (L40S, 4090, ...) with your network volume attached, tells you the moment it
succeeds, and stops the pod automatically so a forgotten pod can't keep billing.

## Quick start
1. Install Python 3.10+ (python.org or `brew install python`). For the app window on Mac: `brew install python-tk@3.14` (match your Python version).
2. Open the app: `python3 gpu_sniper.py gui`  (or use the packaged app from the build step below).
3. Paste your RunPod API key, click **Load volumes**, pick your volume, enter your template ID.
4. Click **Show GPUs** to see which GPUs exist in your volume's data center (with price and availability), and list them in priority order.
5. **Test alerts** sends a test notification to everything you configured.
6. Click **Dry run** first (creates nothing; it also validates every setting against RunPod), then **Start**.
7. When a pod is secured you get a notification, and a second one when ComfyUI is actually answering. **Open ComfyUI** opens it.

Terminal alternative: `python3 gpu_sniper.py setup`, then `run` (dry run), `deploy`, and `gpus` (availability list).
`python3 gpu_sniper.py stop` stops every running pod that uses your volume.

## Safety
- Pods bill the moment they are created. Set **Auto-stop after (hours)** and/or **Auto-stop when idle (minutes)**.
- Idle detection watches ComfyUI's queue on the pod (`/queue`); it only counts once the pod answers.
- Auto-stop only works while the app/terminal stays open. The app keeps your computer awake while it works (toggle in the form).
- Only one search can run at a time (a lock file stops a second window from double-booking pods).

## What it tells you instead of crashing
Low RunPod balance, wrong template ID, unknown GPU name (with suggestions), GPU not offered in your volume's
data center, template with a conflicting persistent disk, GPU pool your account can't use, rate limits
(honors Retry-After), RunPod outages (retries with backoff), network drops, a pod stopped from the console,
and a pod whose ComfyUI never answers (idle detection then falls back to the runtime cap).
- Your API key is stored in `~/.gpu_sniper/config.json` (owner-only permissions) and is only sent to api.runpod.io.
- Log file: `~/.gpu_sniper/sniper.log`.

## Alerts
Mac notification always (on macOS). Optional phone alerts via ntfy.sh (install the ntfy app, subscribe to a
private topic name, put that name in the app) and/or a Discord webhook.

## Building a double-clickable app
- Mac: `bash build_mac.sh`  ->  `dist/GPU Sniper.app`
- Windows: `build_windows.bat`  ->  `dist\GPU Sniper.exe`
Build on each operating system you want to ship for.

## Selling it — see LAUNCH_CHECKLIST.md (edit the top of gpu_sniper.py)
- `LICENSE_REQUIRED = True`, plus `LICENSE_PRODUCT_ID`: checks keys with the Lemon Squeezy License API
  (activate once, re-validate later, 7-day offline grace). Create the product with license keys enabled.
- `UPDATE_URL`: host a small JSON file `{"version": "3.2.0", "url": "https://your-page"}`; the app tells users when a newer version exists.
- Mac apps need code signing/notarization to open without warnings (Apple Developer account); unsigned apps
  open via right-click > Open.
- Keep a clear refund policy and tell buyers it uses their own RunPod key and that RunPod bills them directly.
