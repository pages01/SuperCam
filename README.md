# SuperCam — Capture Lab

SuperCam is a local-first, single-page camera experiment designed to run from GitHub Pages or another HTTPS host.

## Deploy / run

**One-time setup:** open [SuperCam → Settings → Pages](https://github.com/pages01/SuperCam/settings/pages). Under **Build and deployment**, set **Source** to **GitHub Actions** and save. The GitHub Actions token connected here cannot create the repository's Pages site setting on its own.

After Pages is enabled, the workflow at `.github/workflows/pages.yml` deploys on pushes to `main`. If the workflow already failed because Pages was not configured, use **Actions → Deploy SuperCam to GitHub Pages → Run workflow** after enabling the setting.

Expected site URL: https://pages01.github.io/SuperCam/

For local testing, serve `index.html` from localhost or HTTPS, grant camera permission, choose a capture mode, then use the settings panel. Directly opening the HTML as a `file://` URL may block camera access.

The app is a static website: it has no server-side capture service and doesn't automatically upload your images. Captures live in the page session until saved or cleared.

## Included in this prototype

- Live camera with front/rear switching where the browser exposes camera selection.
- Requested stream resolution and frame rate, with actual stream settings shown.
- Camera capability detection for zoom, exposure compensation, focus distance, torch, still capture, and video recording.
- Photo capture; JPEG, PNG, and WebP export options, subject to browser encoding support.
- Browser-based video recording with supported MIME-type detection and configurable target bitrate.
- Multi-frame averaging / denoise stack, with experimental 2× shift-and-add reconstruction and adjustable frame count (2–300).
- Exposure-bracket attempt and tone-mapped HDR-style fusion fallback.
- Long-duration frame compositing: maximum-brightness light paths, temporal-average long exposure, and a noise-average stack.
- Live RGB histogram, composition grid, level reference, and experimental focus-edge overlay.
- Local capture roll with open/save actions.

## Important technical boundaries

This is a browser camera app, not a native camera driver.

- **Apple ProRAW / DNG sensor RAW:** not available through the standard browser camera APIs. SuperCam explicitly does not label JPEGs as RAW. True Apple ProRAW requires a native iOS capture pipeline using Apple's AVFoundation.
- **True optical shutter time, native sensor gain, sensor readout, multi-lens selection, 10-bit HDR and Dolby Vision:** only exposed where the platform's APIs make them available. The standard browser camera stream doesn't expose all of the controls from Apple's native camera stack.
- **Super-resolution:** the multi-frame mode samples frames, performs small translation registration, combines the samples, and can output a 2× reconstruction. It is experimental and is not comparable to a dedicated ISP or trained super-resolution model. Scene movement, camera motion, autofocus, exposure changes, rolling shutter, and browser downscaling can create artifacts.
- **HDR:** when exposure compensation is available, the app attempts a bracket. Otherwise it reports the fusion fallback. The output is an ordinary tone-mapped image, not a RAW HDR master.
- **Long exposure / light paths:** these are frame-compositing effects over the selected duration, not a single sensor exposure lasting that entire time.
- **Resolution/FPS:** requested stream settings are best-effort; the actual negotiated stream is displayed. Video codecs and bitrates are device-dependent.

## Privacy

Camera and microphone access are requested only by the browser. Microphone recording is opt-in. Captures are processed in-page and stored in memory until downloaded; clearing or closing the page removes the session gallery.

## Current scope

This is an experimental first release intended for iterative development. Priorities for a native follow-up would include true RAW capture, platform photo-pipeline integration, gyro-assisted frame registration, robust motion rejection, and native HDR/video controls.
