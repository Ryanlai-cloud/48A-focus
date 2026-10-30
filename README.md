# 48A Focus · 航班专注计时器

> 把每一次专注，变成一段航程。
> *Turn every focus session into a flight.*

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](LICENSE.md)
![Single file](https://img.shields.io/badge/build-single--file%20HTML-success)
![Runtime deps](https://img.shields.io/badge/runtime-0%20dependencies-blue)

![登机牌界面 / Boarding screen](demo/boarding.png)

## 简介 / About

**48A Focus** 是一个航班主题的专注计时器。打开页面会先收到一张登机牌，选定语言与目的地后按下「开始专注」，镜头便从太空俯冲至地球，沿大圆航线飞向目的地，倒计时结束即「抵达」。

本项目**基于 [Flight PTV](https://github.com/yoki0102/flight-ptv)（作者 [yoki0102](https://github.com/yoki0102)）改造而来**，并非原创。3D 地球、昼夜着色器、目的地卷轴、两段式镜头运镜与波音 777 模型均继承自上游；本仓库主要新增了专注计时流程、发餐/休息环节、防切屏机制，并把整个应用打包成一个可双击运行的单文件。

> **English:** A flight-themed focus timer. You get a boarding card, pick a language and a destination, then the camera dives from space to the globe and flies the great-circle route while your focus session counts down. Built on top of [Flight PTV](https://github.com/yoki0102/flight-ptv) by yoki0102 — see [Differences from upstream](#与上游-flight-ptv-的差异--differences-from-upstream).

---

## 🔗 在线体验 / Live Demo

**https://YOUR_USERNAME.github.io/48A-focus/**

> 首次加载需要几秒：单文件里内联了约 **14 MB** 的地球贴图、星空、波音 777 模型（Draco）、登机音乐与 Draco 解码器。加载完成后**零外部请求**，断网也能用。

---

## ✨ 功能 / Features

- **登机牌式开场**：座位号 `48A`、出发地、飞行日志入口，点右下箭头进入设置
- **三段式流程**：语言（简中 / 繁中 / English）→ 目的地（70+ 亚洲及全球航点）→ 开始专注
- **专注即航班**：专注时长由航线距离按 724 km/h 推算；广州 `CAN` 为 3 分钟「测试航线」，适合快速预览
- **实时昼夜地球**：按当前 UTC 计算真实太阳直射点，`?utc=6` 可快进观察晨昏线
- **航线动画**：大圆航线弧线 + 波音 777 沿弧线飞行，镜头在弧线长成后交接到地平线视角
- **发餐 / 休息环节**：剩余时间进入休息窗口时，切换为全屏白底黑字的「现在是发餐环节，休息一下吧！」+ 倒计时；休息时长从专注总时长**内部划出**，按 T 分五档（见下表）
- **防切屏 FocusGuard**：双层判定 —— 页面可见性事件 + 可选 Chrome 扩展上报当前活动标签页域名；命中白名单域名才算「合法离开」，否则立即「迫降」（专注失败，不记入日志）
- **长按 R 放弃专注**：按住 15 秒、带进度条，松手即取消；放弃的航程不写入飞行日志
- **飞行日志**：localStorage 记录每次航程的航线、里程、时长、登机时间，并累计总里程 / 总时长 / 次数
- **单文件分发**：`index.html` 自包含 CSS / JS / 图片 / 模型 / 音频，双击即用，可离线
- **快捷键**：`Space` / `P` 暂停继续、按住 `R` 15 秒放弃、`F` 全屏、`M` 调试面板（仅 `?dev=1`）

---

## 📸 截图 / Screenshots

| ![目的地揭晓](demo/reveal.png) | ![飞行中](demo/flight.png) |
| :---: | :---: |
| **目的地揭晓 / Destination reveal** | **飞行中 · 波音 777 沿大圆航线 / In flight** |

| ![发餐休息](demo/rest.png) |
| :---: |
| **发餐 / 休息环节（全屏白底黑字 + 倒计时）** |

---

## 🚀 快速开始 / Quick Start

**方式一：直接打开（推荐）**

```bash
git clone https://github.com/YOUR_USERNAME/48A-focus.git
cd 48A-focus
open index.html      # macOS
# xdg-open index.html  # Linux
# start index.html     # Windows
```

**方式二：本地服务器**（某些浏览器对 `file://` 的权限限制较严）

```bash
python3 -m http.server 8768
# 浏览器打开 http://localhost:8768/
```

> 防切屏扩展只对 `http://localhost` / `http://127.0.0.1` 生效，本地服务器方式体验最完整。

---

## 🛡 防切屏 / FocusGuard

飞行中切到非白名单页面会被判为「迫降」。判定分两层，装了扩展更严格：

| 层级 | 实现 | 效果 |
| --- | --- | --- |
| 第一层（默认开启） | `visibilitychange`：标签页隐藏 / 窗口失焦 | 记录离开次数；返回后若判定违规即迫降 |
| 第二层（可选） | `extension/` 中的 Chrome MV3 扩展，上报当前活动标签页 hostname | 精确到「切到了哪个网站」，非白名单立即迫降 |

白名单匹配规则：**hostname 完全相等，或点号后缀匹配**（`a.notion.com` 命中 `notion.com`，而 `notnotion.com` 不会误命中）。默认白名单覆盖学习类（MOOC、Notion、飞书、Google Docs、DeepSeek 等）与工作类（钉钉、腾讯文档、Canva 等），可在 [`src/app.jsx`](src/app.jsx) 的 `ALLOWED_HOSTS` 中增删。

页面通过全局接口 `window.FocusGuard` 接收上报（`registerExtension` / `reportActiveTab` / `whitelist` / `state`），扩展侧**不保存白名单**。

**安装扩展：**

1. Chrome 打开 `chrome://extensions/`
2. 打开右上角「开发者模式」
3. 「加载已解压的扩展程序」→ 选择本仓库的 `extension/` 目录
4. 刷新专注页面，状态栏会显示「扩展精确校验已启用」

---

## ⏱ 休息档位 / Rest tiers

以单段航线理论时长 **T** 为总专注时长，休息从 T 内部划出（不额外叠加），学习时长 = T − 休息。

| 档位 | 总时长 T | 休息 | 说明 |
| --- | --- | --- | --- |
| A | ≤ 90 分钟 | 无 | 一口气飞完 |
| B | 90–180 分钟 | 15 分钟 | |
| C | 180–360 分钟 | 30 分钟 | |
| D | 360–600 分钟 | 45 分钟 | |
| E | > 600 分钟 | 60 分钟 | 须覆盖午饭 11:30–13:30 或晚饭 17:30–19:30 |

**升档规则**：连续 3 个 B/C 航段的第 3 段休息升为 60 分钟并重新计数；同一天第二段 D 升为 60 分钟（其中至少 20 分钟不碰学习材料）；每两段 E 的第二段升为 90 分钟且必须含一顿正餐；C/D 档结束时间落在 12:00–13:00 或 18:00–19:00 时可按 E 档逻辑延长至 60 分钟。

---

## ⌨️ 快捷键 / Shortcuts

| 按键 | 作用 |
| --- | --- |
| `Space` / `P` | 暂停 / 继续（所有动画与倒计时同步冻结） |
| 按住 `R` 15 秒 | 放弃本次专注（带进度条，松手取消，不记入日志） |
| `F` | 全屏切换 |
| `M` | 显示 / 隐藏调试面板（需 `?dev=1`） |

**调试参数**：`?dev=1` 打开 Tweaks 面板；`?utc=6` 固定太阳直射时刻；`?restdemo=1` 把航程压缩成 25 秒 + 20 秒休息，快速预览发餐界面。

---

## 🛠 从源码构建 / Build

产物 `index.html` 已提交在仓库根目录，通常无需构建。修改 `src/` 后重新生成：

```bash
npm install            # 安装 esbuild（唯一构建依赖）
npm run build          # 等价于 bash build.sh：打包 + 组装单文件
```

`build.sh` 会先由 `src/index.jsx`（依次引入 `tweaks-panel.jsx` / `earth.jsx` / `app.jsx`）打包出 `build/app.js`，再由 `tools/build.cjs` 把模板、Three.js、React、业务代码与全部媒体资源（base64）注入 `src/index.template.html`，输出自包含的 `index.html`（约 14 MB）。

**技术栈**：React 18 (UMD) · Three.js r147 · 自定义 GLSL · DRACOLoader · esbuild · WebP 贴图 · Line2 弧线

---

## 📁 目录结构 / Layout

```text
.
├── index.html                 # 自包含单文件产物（14 MB，零外部依赖）
├── build.sh                   # 构建脚本（esbuild + 单文件组装）
├── src/
│   ├── index.template.html    # HTML 模板（含 @INLINE 标记）
│   ├── index.jsx              # 入口
│   ├── app.jsx                # 主应用：流程、计时、发餐、FocusGuard、日志
│   ├── earth.jsx              # 3D 地球 / 航线 / 飞机 / 镜头
│   └── tweaks-panel.jsx       # 调试面板
├── tools/build.cjs            # 资源内联与单文件组装
├── assets/                    # 贴图、模型、音频（构建输入）
├── vendor/                    # Three.js / React / Draco（库源文件）
├── extension/                 # 可选 Chrome MV3 扩展（FocusGuard 精确校验）
└── demo/                      # README 截图
```

---

## 🔀 与上游 Flight PTV 的差异 / Differences from upstream

| | 上游 Flight PTV | 本项目 48A Focus |
| --- | --- | --- |
| 定位 | 影音级 PTV 演示 | 专注 / 番茄钟计时器 |
| 流程 | 打开即播，`M` 面板调参 | 登机牌 → 语言 → 目的地 → 专注倒计时 |
| 计时 | 无 | 航线距离推算时长，倒计时驱动飞行进度 |
| 休息 | 无 | 发餐/休息全屏界面 + A–E 档位与升档规则 |
| 防切屏 | 无 | FocusGuard 双层判定 + 白名单 + 迫降界面 |
| 放弃 | 无 | 长按 R 15 秒 |
| 日志 | 无 | 本地飞行日志与累计统计 |
| 交付 | 多文件站点 | 单文件 `index.html`，可离线双击运行 |

继承自上游且基本未改的部分：3D 地球与昼夜 GLSL 着色器、目的地卷轴动画、两段式镜头运镜、波音 777 模型与航线弧。

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

## ⚠️ 免责声明 / Disclaimer

本项目为独立爱好者作品，**与 Cathay Pacific Airways Limited（国泰航空）无任何关联，亦未获其认可或赞助**。文中出现的商标、品牌名称均归各自所有者所有。

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
