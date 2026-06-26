# Ajokoe — Finnish Driving Theory Trainer

A single-page web app to practice for the Finnish driving theory test (Traficom format) and the taxi entrepreneur exam (taksiliikenteen yrittäjäkoe).

**Features**
- 🖼️ **Traffic situations** — 100+ hand-drawn driver's-eye scenes, answered Yes/No. Practice serves a **random set of 30** each time (≈86% to pass).
- 📋 **Multiple choice** — 100+ questions across 16 topics.
- ⚠️ **Risk identification** — developing-hazard scenarios.
- 🪧 **Road-sign gallery** — 60+ signs (browse + quiz).
- ⏱️ **Full mock exam** — official 15 + 5 + 50 structure with per-section pass limits and a 30-minute timer.
- 🚕 **Taxi entrepreneur exam** — 50 questions across the 10 official topic areas, 2-hour mock, ≥35/50 to pass.

Everything runs in the browser. Progress is in-memory (a page reload resets scores).

## Use it as a website on GitHub Pages

1. Create a new repository on GitHub (e.g. `ajokoe`).
2. Upload **`index.html`** (and optionally this `README.md`) to the repository root.
3. Go to **Settings → Pages**.
4. Under **Build and deployment → Source**, choose **Deploy from a branch**.
5. Select branch **`main`** and folder **`/ (root)`**, then **Save**.
6. Wait ~1 minute. Your app is live at `https://<your-username>.github.io/<repo-name>/`.

No build step is needed — `index.html` is fully self-contained (it loads React and Babel from a CDN, so the device needs an internet connection on first load).

## Disclaimer
Practice material only. The official Traficom/exam questions are not public; these drill the same rules and topics. Verify current figures (tax rates, limits, dates) before your exam.
