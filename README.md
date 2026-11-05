# 48A Focus · Flight Focus Timer

> Turn every focus session into a flight.

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](LICENSE.md)
![Single file](https://img.shields.io/badge/build-single--file%20HTML-success)
![Runtime deps](https://img.shields.io/badge/runtime-0%20dependencies-blue)

## About

**48A Focus** is a flight-themed focus timer. The page opens with a boarding pass. After you pick a language and a destination and press "Start Focus", the camera dives from space down to the globe, flies the great-circle route toward your destination, and "arrives" when the countdown ends.

This project is a modified version of **[Flight PTV](https://github.com/yoki0102/flight-ptv)** by [yoki0102](https://github.com/yoki0102). It is not original work. The 3D Earth, the day/night shader, the destination reveal, the two-stage camera flythrough, and the Boeing 777 model are inherited from upstream. This repository mainly adds the focus-timer flow, the meal/rest break, the anti-tab-switching mechanism, and packaging of the whole app into a single double-clickable file.

---

## Live Demo

The demo is hosted on GitHub Pages. First load takes a few seconds: the single file inlines roughly **14 MB** of Earth textures, starfield, the Boeing 777 model (Draco), boarding music, and the Draco decoder. Once loaded, there are **zero external requests** and the page works offline.

---

## Features

- **Boarding-pass opening**: seat `48A`, origin, flight-log entry; tap the bottom-right arrow to enter setup
- **Three-step flow**: language (Simplified Chinese / Traditional Chinese / English) → destination (70+ Asian and global waypoints) → start focus
- **Focus as a flight**: focus duration is derived from route distance at 724 km/h; Guangzhou `CAN` is a 3-minute "test route" for quick previews
- **Real-time day/night Earth**: the actual sub-solar point is computed from current UTC time; `?utc=6` fast-forwards to observe the terminator
- **Route animation**: great-circle arc plus a Boeing 777 flying along it; the camera hands off to a horizon view once the arc has grown
- **Meal / rest break**: when the remaining time enters the rest window, the screen switches to a full-screen white background with black text — "It's meal time, take a break!" — plus a countdown. Rest time is carved **out of** the total focus duration, in five tiers based on T (see below)
- **FocusGuard anti-tab-switching**: two-layer detection — page visibility events plus an optional Chrome extension reporting the active tab's hostname; only whitelisted hostnames count as a "legal leave", otherwise an immediate "forced landing" occurs (focus failed, not recorded in the log)
- **Hold R to abandon**: hold for 15 seconds with a progress bar; release cancels. Abandoned flights are not written to the flight log
- **Flight log**: localStorage records each flight's route, distance, duration, and boarding time, and accumulates total distance / total time / count
- **Single-file distribution**: `index.html` is self-contained (CSS / JS / images / model / audio); double-click to run, works offline
- **Shortcuts**: `Space` / `P` pause/resume, hold `R` for 15 seconds to abandon, `F` fullscreen, `M` debug panel (only with `?dev=1`)

---

## 🎨 Credits

Built on the work of others — please retain these credits if you fork or adapt this project.

- **Original project — [Flight PTV](https://github.com/yoki0102/flight-ptv)** by [yoki0102](https://github.com/yoki0102), licensed under [CC BY-NC 4.0](LICENSE.md). This repository is a modified version of it.
- **Boeing 777-300ER 3D model** by [hakai315](https://sketchfab.com/hakai315), licensed under [CC-BY-4.0](http://creativecommons.org/licenses/by/4.0/).
  *This work is based on ["Boeing 777-300ER Model"](https://sketchfab.com/3d-models/boeing-777-300er-model-322e7961d2024bba834887878d2d49a2) by hakai315.*
- **Earth day/night imagery** based on NASA's [Visible Earth Blue Marble](https://visibleearth.nasa.gov/collection/1484/blue-marble) dataset (public domain).
- **Milky Way panorama** — 8K equirectangular starfield from NASA/ESO imagery (public domain).
- **Ubuntu** and **Ubuntu Condensed** fonts © Canonical Ltd., licensed under the [Ubuntu Font License](https://ubuntu.com/legal/font-licence).
- **Chiron GoRound TC** font by Sora Sagano, licensed under [SIL Open Font License 1.1](https://scripts.sil.org/OFL).
- **Three.js**, **React**, and **esbuild** — MIT.

Visual and interaction design inspired by the seatback PTV systems on **Cathay Pacific** long-haul flights.

---

##  Disclaimer


This is an independent fan project, **not affiliated with, endorsed by, or sponsored by Cathay Pacific Airways Limited**. All third-party trademarks remain the property of their respective owners.

---

## 📄 License

The original code and the animation it produces are licensed under
**[Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](LICENSE.md)**.
This repository is a modified version of *Flight PTV* by yoki0102 and keeps the same license.

**You may** use, study, modify, and share this project for **non-commercial** purposes, as long as you give credit and link back to both repositories.

**You may not** use it for any **commercial** purpose — including in a paid product or service, in advertising, or in promotional material — without separate written permission.

**Attribution example** (keep this when you share or adapt the work):

> Based on "Flight PTV" by yoki0102 — https://github.com/yoki0102/flight-ptv (CC BY-NC 4.0).
> Modified as "48A Focus" — https://github.com/YOUR_USERNAME/48A-focus.

Bundled third-party assets (the Boeing model, fonts, NASA imagery, React, Three.js, esbuild, etc.) keep their **own original licenses** — see Credits above. This NonCommercial license applies only to the original work.
