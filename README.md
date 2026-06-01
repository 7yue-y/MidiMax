# 🎹 MIDIMax — Nopia SynthLab

> 智能和弦引擎 MIDI 控制器，将单音自动展开为和弦，通过虚拟 MIDI 端口驱动 Reaper 中的 VSTi 音源。

---

## ✨ 功能特性

### 🖥️ 运行平台
- **双模运行**：既可以直接在 Chrome/Edge 等浏览器作为 **Web 网页应用** 访问，也能打包为精简的 **Tauri 2.0 桌面客户端**（独立执行程序，低延迟、低硬件消耗，体积仅 ~10MB 左右）。

### 🎵 和弦引擎
- **调性选择**：12 个调性（C ~ B）一键切换
- **Tension 渐进**：7 级和弦复杂度（单音 → 十三和弦）
- **挂留和弦**：Sus2 / Sus4 一键切换
- **Add 模式**：三和弦 + 最高扩展音
- **V Major**：强制属和弦大调化

### 🎹 输入方式
- **实体 MIDI 键盘**：通过 WebMidi 直接接入
- **屏幕虚拟键盘**：鼠标点击演奏，支持力度响应（点击位置越靠下力度越大）
- **八度切换**：▲▼ 按钮切换键盘音域（C1 ~ C7）

### 🔀 多通道 MIDI 路由
| 通道 | 用途 | 说明 |
|------|------|------|
| CH1 | 和弦钢琴 | 和弦引擎生成的完整和弦 |
| CH2 | Bass 低音 | 根音/三音/五音（归一化到低八度） |
| CH3 | Synth/琶音 | 直通模式或琶音模式 |

### 🎼 琶音器
- 7 种速率：4n. / 4n / 4t / 8n / 8t / 16n / 16t
- ±3 八度偏移
- Legato 无缝切换和弦
- Tap Tempo BPM 同步

### 🎚️ 其他
- Bass 自动跟随（AUTO / TONE / SLOW 模式）
- Pitch Bend 弯音轮
- 转位控制（1st / 2nd / Open）
- 所有参数自动保存到 localStorage

---

## 🚀 快速开始

项目支持作为 **Web 浏览器应用** 或 **Tauri 桌面客户端** 运行。

### 前置要求

1. **Node.js** >= 18
2. **loopMIDI**（虚拟 MIDI 端口，Windows 用户推荐）
   - 安装命令：`winget install TobiasErichsen.loopMIDI`
   - 启动 loopMIDI，确保列表中有一个虚拟端口（默认会自动创建 `loopMIDI Port 1`）
3. **Chrome / Edge 浏览器**（如果运行 Web 版本，需要 WebMidi 接口支持，Safari 不支持）
4. **Rust 编译环境**（仅当开发或构建 Tauri 桌面版时需要）
   - 请参考 [Tauri 官方安装指南](https://tauri.app/start/prerequisites/) 配置 Rust/Cargo 环境。

### 1. 安装项目依赖

在项目根目录下执行：
```bash
npm install
```

### 2. 运行与开发

#### 🌐 方式 A：Web 浏览器版本
运行以下命令启动 Vite 开发服务器：
```bash
npm run dev
```
启动后在支持 WebMidi 的浏览器（如 Chrome/Edge）中访问控制台提示的地址（通常为 `http://localhost:5173/`）。

#### 💻 方式 B：Tauri 桌面客户端
运行以下命令启动 Tauri 桌面端开发调试：
```bash
npm run tauri dev
```
此命令将编译 Rust 后端并拉起独立的桌面应用窗口，支持热重载。

### 3. 构建与打包

#### 🌐 Web 浏览器版本打包
```bash
npm run build
```
打包后的静态资源输出在 `dist/` 目录中。

#### 💻 Tauri 桌面端打包
```bash
npm run tauri build
```
编译并生成平台原生的桌面安装包（如 Windows 下的 `.msi` 安装包及独立 `.exe` 程序），输出在 `src-tauri/target/release/bundle/` 目录下。

### 4. 配置 MIDI 设备

无论是 Web 版还是桌面客户端，运行后均需在页面顶部工具栏进行如下配置：
- **🎹 MIDI 输入** → 选择你的实体 MIDI 键盘（可选，若使用屏幕虚拟键盘则无需选择）
- **🔊 MIDI 输出** → 选择 `loopMIDI Port 1`（用于将生成的 MIDI 信号路由到 DAW 音源）

---

## 🎧 Reaper 配置

### 1. 启用 MIDI 输入设备

`Options → Preferences → MIDI Devices`

在 MIDI inputs 列表中找到 `loopMIDI Port 1` → 双击 → 勾选 `Enable input from this device`

### 2. 创建轨道

推荐创建 3 条轨道分别接收不同通道：

| 轨道 | MIDI 输入设置 | 用途 | 建议 VSTi |
|------|-------------|------|----------|
| 轨道 1 | `MIDI → loopMIDI Port 1 → Channel 1` | 和弦钢琴 | Kontakt / Piano One |
| 轨道 2 | `MIDI → loopMIDI Port 1 → Channel 2` | Bass | Bass 音源 |
| 轨道 3 | `MIDI → loopMIDI Port 1 → Channel 3` | Synth/Arp | Serum / Vital |

### 3. 开启监听

每条轨道：
1. 点击 **录音 Arm** 按钮（红色圆圈）
2. 开启 **Monitor** 按钮（小喇叭图标）
3. 在 FX 中加载你的 VSTi 音源

### 4. 开始演奏

在 MIDIMax 中点击钢琴键或用 MIDI 键盘弹奏 → Reaper VSTi 发声 🎶

---

## 🎛️ 界面说明

```
┌──────────────────────────────────────────────────────────────────┐
│ 🎹 MIDI 输入: [下拉]  🔊 MIDI 输出: [下拉]  🔗 Sidechain: [下拉] │  ← MIDI 设备选择
├──────────────────────────────────────────────────────────────────┤
│  [旋钮][旋钮] [<]   [120bpm] [■][■][■][■][■][■]  [■][■][■]     │  ← 顶部控制栏
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│   BASS            TENSION              SYNTH                     │
│  ┌───┐            ┌─────┐             ┌─────┐                   │
│  │ V │  AUTO     │     │  Add        │     │  [竖推]            │
│  │Root│  TONE  [旋钮][大旋钮]  Sus2  V Major  [旋钮]             │
│  │III │  SLOW    │     │  Sus4                                   │
│  └───┘            └─────┘             Arp  Octave [滑块] Oct 0   │
│                                                                  │
│  [弯音]  ┌─────────────────────┐  1st   TONAL SELECT             │
│  [弯音]  │   🎹 钢琴键盘        │  2nd  [C#][D#]  [F#][G#][A#]   │
│    ▲     │   (点击演奏)         │ Open  [C][D][E][F][G][A][B]    │
│   C4     └─────────────────────┘                                 │
│    ▼                                                             │
└──────────────────────────────────────────────────────────────────┘
```

---

## 🔧 信号流

```
MIDI 键盘 ──┐
             ├──→ WebMidi Input ──→ 和弦引擎 ──→ WebMidi Output ──→ loopMIDI ──→ Reaper VSTi
屏幕键盘 ──┘                        (纯数学)
```

- **不使用 OSC**
- **不需要 ReaLearn**
- **端到端延迟 ~3-5ms**

---

## 📁 项目结构

```
├── src/                        # Web 前端与核心引擎源码
│   ├── main.ts                 # 应用入口
│   ├── App.vue                 # 根组件
│   ├── style.css               # 全局样式
│   ├── engine/
│   │   └── useChordEngine.ts   # 核心和弦引擎（负责和弦映射、音符生成）
│   ├── store/
│   │   ├── midi.ts             # MIDI 设备管理仓库
│   │   └── controls.ts         # 控件状态持久化仓库
│   └── components/
│       ├── SynthLab.vue        # 核心合成器界面（包含所有功能控制模块）
│       ├── PianoKeys.vue       # 键盘可视化按键（带力度与范围响应）
│       ├── MidiDeviceSelector.vue # 设备输入输出下拉菜单
│       └── controls/
│           ├── Button.vue      # 统一样式按钮组件
│           ├── Knob.vue        # 旋钮控件组件
│           ├── Slider.vue      # 纵向滑块组件
│           └── PitchBender.vue # 弯音轮与调制轮组件
└── src-tauri/                  # Tauri 2.0 桌面端工程配置文件及 Rust 后端壳体
```

---

## 🔗 技术栈

| 技术 | 用途 |
|------|------|
| Vue 3 + TypeScript | Web UI 前端框架与逻辑实现 |
| Vite | 极速前端构建/开发服务器与打包工具 |
| Pinia | 跨组件与持久化的全局状态管理 |
| WebMidi.js | 原生 WebMidi 接口的高级封装与 MIDI 设备读写 |
| TailwindCSS | 原子化样式系统，精确像素级控制和自适应排版 |
| Tauri 2.0 + Rust | 桌面端跨平台引擎，提供极小包体积和原生低延迟路由 |

---

## 📜 来源

从 [ReaMax](https://github.com/your-repo/ReaMax) 项目的 SynthLab (Nopia) 模块独立提取。

---

## 📄 版权声明

本项目目前**保留所有权利**（All Rights Reserved）。未经作者明确书面许可，任何人不得对本项目的全部或部分代码进行二次分发、商业使用或修改后闭源发布。

[text](DOC/使用说明.md)