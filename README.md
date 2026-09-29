# ✦ Pulsar Client

**Pulsar Client** is an open-source, hyper-lightweight Minecraft optimization client and launcher built around a strict "No-BS" philosophy: maximizing raw FPS, eliminating micro-stutters, and delivering an ultra-clean interface with zero corporate adware, zero trackers, and zero background bloat.

---

## ⚡ What is Pulsar Client?

Pulsar Client is a modern, performance-focused alternative to traditional Minecraft launchers:

* **🚀 Sub-Second Startup:** Native Rust + Tauri v2 backend ensures sub-second boot times and an idle footprint under **30MB RAM**.
* **🎮 Pre-Tuned Performance Stack:** Automatically injects Fabric Loader along with essential optimization mods (*Sodium*, *NVIDIUM*, *Iris*, *Lithium*, *FerriteCore*, and *ModernFix*).
* **🔒 Secure Local Auth:** Microsoft OAuth 2.0 PKCE authentication runs via a local `127.0.0.1` loopback server—tokens never leave your machine.
* **🛡️ 0% Telemetry:** 100% open source with zero telemetry, zero trackers, and no daily ad carousels.

---

## 🛠️ Stack Architecture

* **Frontend:** Vite • React • TypeScript • Tailwind CSS • Shadcn UI
* **Backend Core:** Rust • Tauri v2 • Tokio
* **Game Engine:** Java JVM + Fabric Loader

---

## 💻 Local Setup

### Prerequisites
* **Node.js** (v18+) & **pnpm**
* **Rust Toolchain** (`rustup default stable`)
* **Java JDK** 17 or 21
