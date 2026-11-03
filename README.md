# Kage — Where Stillness Reveals the Unseen ⛩️

> A five-chapter night walk through a Kyoto mountain temple. Charred cypress, lantern light, and a vermilion moon, rendered live in WebGL and Three.js.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Built with: Three.js](https://img.shields.io/badge/Built%20with-Three.js-black?logo=three.js)](https://threejs.org/)
[![CSS: Modern Tokens](https://img.shields.io/badge/CSS-Tokens%20%26%20Clamp-red)](css/style.css)

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Styling & CSS Architecture](#-styling--css-architecture)
  - [Design Tokens](#design-tokens)
  - [Responsive Typography & Clamp](#responsive-typography--clamp)
  - [Glassmorphism & Sticky Navigation](#glassmorphism--sticky-navigation)
- [Smooth Scrolling & Animation Mechanics](#-smooth-scrolling--animation-mechanics)
  - [Frame-rate Independent Damping (`damp`)](#1-frame-rate-independent-damping-damp)
  - [Hermite Smoothstep Interpolation (`smooth`)](#2-hermite-smoothstep-interpolation-smooth)
  - [Scroll-Driven 3D Camera Rig](#3-scroll-driven-3d-camera-rig)
  - [Interactive Cloth Canvas Brushing](#4-interactive-cloth-canvas-brushing)
- [Web Audio Ambient Soundscape](#-web-audio-ambient-soundscape)
- [Quick Start / Local Setup](#-quick-start--local-setup)

---

## 🌙 Overview

**Kage** is an immersive digital sanctuary and creative web experience exploring a secluded Kyoto mountain temple after dusk. Everything is rendered live at runtime with:

- **2.5D / 3D Parallax Sanctuary**: 14 hand-crafted WebP layered illustrations integrated into a live Three.js depth camera system.
- **Physics-based Scroll Smoothing**: Smooth Hermite curves and exponential damping for stutter-free scrolling across displays of all refresh rates (60Hz, 120Hz, 144Hz ProMotion).
- **Procedural Audio**: Real-time synthesized temple winds and meditative bells generated via the Web Audio API without heavy audio files.
- **Zero Framework Bloat**: Pure vanilla HTML5, CSS3, and ES6+ JavaScript.

---

## 🛠️ Tech Stack

| Technology | Purpose | What It Does In The Project |
| :--- | :--- | :--- |
| **HTML5** | Markup & Structure | Semantic chapter layout, accessible headings, navigation rails, and backdrop canvas hooks. |
| **CSS3** | Visual Design & Layout | Design tokens (variables), fluid typography (`clamp`), backdrop filters, custom `@font-face`, and dark mode aesthetics. |
| **Three.js (WebGL)** | 3D Graphics Engine | Powers the background depth canvas, perspective camera, floating ember particles, and z-plane scene layers. |
| **Web Audio API** | Procedural Audio | Generates ambient wind noise and resonant temple bell tones purely through browser oscillators and biquad filters. |
| **JavaScript (ES6+)** | Interaction & Physics | Frame-rate independent interpolation, scroll progress calculation, active chapter observer, and pointer tracking. |
| **Node.js** | Local Server | Zero-dependency, lightweight static server for instant local development. |

---

## 📁 Project Structure

```text
kage/
├── index.html               # Main entrypoint and semantic chapter structure
├── server.js                # Zero-dependency local Node.js static server
├── package.json             # Project metadata and run scripts
├── README.md                # Comprehensive documentation and technical guide
├── css/
│   ├── style.css            # Design tokens, chapter layouts, cards, and transitions
│   └── fonts.css            # Custom @font-face declarations
├── js/
│   ├── three.min.js         # Three.js 3D WebGL runtime library
│   └── main.js              # Three.js camera rig, smooth scroll damping, audio synthesizer
└── assets/
    ├── fonts/               # Custom web fonts (Onest, NotoJP, Wordmark)
    │   ├── Onest-Light.woff2
    │   ├── Onest-Regular.woff2
    │   ├── Onest-Medium.woff2
    │   ├── Onest-Bold.woff2
    │   ├── NotoJP-Regular.woff2
    │   ├── Wordmark-Medium.woff2
    │   └── Wordmark-SemiBold.woff2
    └── images/              # 14 WebP layered scene illustrations & icons
        ├── basalt-stones.webp
        ├── garden-bush.webp
        ├── hill.webp
        ├── maple-leaves.webp
        ├── pine-tree.webp
        ├── sakura-branch.webp
        ├── shrine-ruins.webp
        ├── stone-lantern.webp
        ├── tall-grass.webp
        ├── temple-wall.webp
        ├── kage-approach.webp
        ├── kage-lantern-court.webp
        ├── kage-moonwater.webp
        ├── kage-sanmon-preview.webp
        ├── preview.jpg
        └── favicon.svg
```

---

## 🎨 Styling & CSS Architecture

### Design Tokens

All colors, elevations, easing curves, and spatial variables are declared as CSS Custom Properties in `:root`:

```css
:root {
  /* Surface & Ink */
  --ink: #05070a;          /* Deep page black */
  --ink-2: #0a0e12;        /* Raised card black */
  --bone: #dfe7e0;         /* Pale sage white for high-contrast text */
  --bone-dim: #aab4ad;     /* Secondary muted bone */
  --muted: #78837c;        /* Tertiary body and label color */

  /* Accents */
  --vermilion: #e0231c;    /* Kyoto Torii gate red */
  --ember: #ff5a3c;        /* Lantern ember highlight */
  --gold: #c9a24a;         /* Subtle sacred gold highlight */

  /* Borders & Dividers */
  --line: rgba(223, 231, 224, 0.13);
  --line-soft: rgba(223, 231, 224, 0.07);

  /* Fluid Spacing & Heights */
  --pad: clamp(20px, 3.4vw, 56px);
  --nav-h: 84px;

  /* Custom Easing Timing Functions */
  --ease: cubic-bezier(0.22, 0.61, 0.36, 1);
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-io: cubic-bezier(0.65, 0, 0.35, 1);
}
```

### Responsive Typography & Clamp

All padding, section spacing, and header font sizes scale seamlessly between mobile viewports and wide 4K screens using CSS `clamp()`:

```css
h1 {
  font-size: clamp(38px, 6.2vw, 92px);
  font-weight: 500;
  letter-spacing: -0.035em;
  line-height: 1.04;
}

.pad {
  padding-inline: var(--pad);
}
```

### Glassmorphism & Sticky Navigation

When scrolling passes the threshold, the header transitions into a frosted glass navigation bar:

```css
header.stuck {
  backdrop-filter: blur(14px) saturate(140%);
  -webkit-backdrop-filter: blur(14px) saturate(140%);
  background: rgba(5, 7, 10, 0.82);
  border-bottom: 1px solid var(--line-soft);
  box-shadow: 0 12px 36px rgba(0, 0, 0, 0.45);
}
```

---

## ⚡ Smooth Scrolling & Animation Mechanics

### 1. Frame-rate Independent Damping (`damp`)

To guarantee smooth camera and element movement regardless of monitor refresh rate (from 60Hz displays to 144Hz high-refresh screens), animations use exponential decay damping instead of fixed step increments:

```javascript
/**
 * Frame-rate independent exponential damping
 * @param {number} cur   - Current animated value
 * @param {number} to    - Target value
 * @param {number} rate  - Speed/tightness of follow
 * @param {number} dt    - Delta time since last frame in seconds
 */
const damp = (cur, to, rate, dt) => {
  return lerp(cur, to, 1 - Math.exp(-rate * dt));
};

const lerp = (a, b, t) => a + (b - a) * t;
```

### 2. Hermite Smoothstep Interpolation (`smooth`)

Transitions between scene chapters and fade animations rely on a 3rd-order Hermite polynomial (`3x² - 2x³`), eliminating abrupt speed changes:

```javascript
/**
 * Smooth Hermite interpolation (smoothstep)
 * @param {number} e0 - Lower edge
 * @param {number} e1 - Upper edge
 * @param {number} x  - Value to interpolate
 */
const smooth = (e0, e1, x) => {
  const t = Math.max(0, Math.min(1, (x - e0) / (e1 - e0)));
  return t * t * (3 - 2 * t);
};
```

### 3. Scroll-Driven 3D Camera Rig

The Three.js scene position is coupled to user scroll via normalized progress calculation:

```javascript
// Calculate normalized scroll position
RIG.prog = progressFor(window.scrollY);

// Smooth the scroll target with delta-time damping
RIG.smooth = REDUCE ? RIG.prog : damp(RIG.smooth, RIG.prog, 5.2, dt);

// Smooth the mouse pointer offset for subtle interactive parallax
RIG.mx = damp(RIG.mx, RIG.tmx, 2.6, dt);
RIG.my = damp(RIG.my, RIG.tmy, 2.6, dt);

// Apply smooth positions to Three.js camera
camera.position.x = RIG.mx * 0.45;
camera.position.y = -RIG.smooth * SCENE_TRAVEL_HEIGHT + (RIG.my * 0.3);
camera.lookAt(0, camera.position.y, -100);
```

### 4. Interactive Cloth Canvas Brushing

Hovering over gallery cards activates an interactive cloth-weave displacement canvas where pointer movement physically rubs against the woven textile texture:

```javascript
fr.addEventListener('pointermove', (e) => {
  const rect = fr.getBoundingClientRect();
  const px = e.clientX - rect.left;
  const py = e.clientY - rect.top;
  
  // Brush the dynamic weave texture
  brushClothPlate(plate, px, py, e.pressure || 0.5);
});
```

---

## 🔔 Web Audio Ambient Soundscape

Kage includes a built-in procedural sound engine powered by the browser's native `AudioContext`:

- **Wind Generator**: Pink noise filtered through low-pass biquad resonance filters with oscillating frequency modulations mimicking mountain breezes.
- **Singing Bell (Suzu / Temple Chime)**: Dual sine-wave oscillators tuned to resonant pentatonic frequencies with natural exponential decay.
- **Zero Asset Overhead**: No heavy MP3 or WAV files need to be downloaded over the network.

---

## 🚀 Quick Start / Local Setup

### Prerequisites

- [Node.js](https://nodejs.org/) (version 16 or newer) or any standard static web server.

### Run with Node.js

```bash
# Start the local server
npm start
```

Open your browser to:
**[http://localhost:3000](http://localhost:3000)**

### Run with Python

```bash
python3 -m http.server 3000
```

### Direct Browser Access

Because all asset paths are relative, you can also open `index.html` directly in any modern browser:

```bash
open index.html
```

---

## 📄 License

MIT License © 2026 [moryagaurav933-commits](https://github.com/moryagaurav933-commits).
All temple illustrations and sound design authored for the Kage experience.
