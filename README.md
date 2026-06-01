# 🎹 MIDIMax — Nopia SynthLab

> 智能和弦引擎 MIDI 控制器，将单音自动展开为和弦，通过虚拟 MIDI 端口驱动 Reaper 中的 VSTi 音源。

---

## ✨ 功能特性

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

### 前置要求

1. **Node.js** >= 18
2. **loopMIDI**（虚拟 MIDI 端口）
   - 安装：`winget install TobiasErichsen.loopMIDI`
   - 启动 loopMIDI，确保有一个虚拟端口（默认会创建 `loopMIDI Port 1`）
3. **Chrome / Edge 浏览器**（需要 WebMidi 支持，Safari 不支持）

### 安装与启动

```bash
npm install
npm run dev
```

浏览器打开 `http://localhost:5174/`

### 配置 MIDI 设备

在页面顶部工具栏：
- **🎹 MIDI 输入** → 选择你的 MIDI 键盘（可选，用屏幕键盘可不设置）
- **🔊 MIDI 输出** → 选择 `loopMIDI Port 1`

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
src/
├── main.ts                     # 应用入口
├── App.vue                     # 根组件
├── style.css                   # 全局样式
├── engine/
│   └── useChordEngine.ts       # 核心和弦引擎
├── store/
│   ├── midi.ts                 # MIDI 设备管理
│   └── controls.ts             # 控件状态持久化
└── components/
    ├── SynthLab.vue            # 主合成器界面
    ├── PianoKeys.vue           # 钢琴键盘（含八度切换）
    ├── MidiDeviceSelector.vue  # MIDI 设备选择器
    └── controls/
        ├── Button.vue          # 按钮
        ├── Knob.vue            # 旋钮
        ├── Slider.vue          # 滑块
        └── PitchBender.vue     # 弯音轮
```

---

## 🔗 技术栈

| 技术 | 用途 |
|------|------|
| Vue 3 + TypeScript | UI 框架 |
| Vite | 构建工具 |
| Pinia | 状态管理 |
| WebMidi.js | MIDI I/O |
| TailwindCSS | 样式 |

---

## 📜 来源

从 [ReaMax](https://github.com/your-repo/ReaMax) 项目的 SynthLab (Nopia) 模块独立提取。
