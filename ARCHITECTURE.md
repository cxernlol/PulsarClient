 # Pulsar Client — Technical Specification & Project Architecture

**Pulsar Client** is an open-source, hyper-lightweight Minecraft optimization client and launcher built around a strict "No-BS" philosophy: maximizing raw FPS, eliminating micro-stutters, and delivering an ultra-clean, desk-setup-ready desktop interface with zero corporate adware, zero trackers, and zero background bloat.

---

## 1. Project Manifesto & Vision

1. **Zero Bloat & Adware:** No launcher ads, no daily promotional carousels, no sponsored bloatware, and zero background telemetry or tracking scripts.
2. **Resource Efficiency:** Launcher idle footprint under 30MB RAM; game engine tuned for 0ms added render latency.
3. **Open-Source & Community-Owned:** 100% open-source codebase (auditable via GitHub CI/CD) ensuring total transparency and community trust.
4. **Pure Client-Side Model (Phase 1):** Completely free and client-side focused for the initial Alpha release—no mandatory backend servers or cloud subscriptions.

---

## 2. Technical Stack Overview

```text
                      ┌──────────────────────────┐
                      │   Vite + React + Shadcn  │  (UI / UX)
                      └─────────────┬────────────┘
                                    │ IPC
                      ┌─────────────┴────────────┐
                      │     Rust + Tauri v2      │  (Native Launcher)
                      └─────────────┬────────────┘
                                    │ Spawns
                      ┌─────────────┴────────────┐
                      │   Java JVM + Fabric Mod  │  (Game Engine)
                      └──────────────────────────┘
```
- Launcher Frontend: Vite + React (TypeScript) + Tailwind CSS + Shadcn UI + Lucide Icons + Framer Motion.
- Launcher Backend: Rust + Tauri v2 (Handles Microsoft OAuth 2.0 PKCE, parallel async asset downloads via Tokio, and JVM process spawning).
- Game Engine: Fabric Loader automatically injected at launch.

Core Optimization Stack:
- Rendering: Sodium, NVIDIUM (NVIDIA Mesh Shaders), Iris (Shaders).
- Memory & Logic: FerriteCore, Lithium, ModernFix.

## 3. Launcher Directory Architecture
- Launcher Frontend: Vite + React (TypeScript) + Tailwind CSS + Shadcn UI + Lucide Icons + Framer Motion.Launcher
- Backend: Rust + Tauri v2 (Handles Microsoft OAuth 2.0 PKCE, parallel async asset downloads via Tokio, and JVM process spawning).Game Engine: Fabric Loader automatically injected at launch.Core Optimization
- Stack:Rendering: Sodium, NVIDIUM (NVIDIA Mesh Shaders), Iris (Shaders).Memory & Logic: FerriteCore, Lithium, ModernFix.3. Launcher Directory ArchitecturePlaintextpulsar-client-launcher/
```text
├── src-tauri/                   # Rust Backend Engine
│   ├── Cargo.toml
│   └── src/
│       ├── main.rs              # App entry point & Tauri IPC commands
│       ├── auth/                # Microsoft OAuth 2.0 loopback server
│       ├── downloader/          # Async asset, library & Fabric downloader (Tokio)
│       ├── game/                # JVM command builder & process management
│       └── config/              # Local settings persistence (RAM, JVM flags)
│
└── src/                         # React Frontend (Vite + Shadcn UI)
    ├── index.html
    └── src/
        ├── App.tsx
        ├── components/
        │   ├── splash/          # Instant frameless morphing loader (<100ms)
        │   ├── auth/            # Microsoft login view (OAuth redirect)
        │   ├── dashboard/       # Main launch screen with centered CTA
        │   ├── layout/          # Auto-hiding hover sidebar drawer
        │   ├── library/         # Game versions, installed mods & resource packs
        │   ├── mods/            # Modrinth open-source mod browser
        │   └── settings/        # RAM slider, JVM flags & theme toggles
        ├── store/               # Zustand state management
        └── types/               # TypeScript interfaces
```
---

## 4. UI / UX Design & Layout Blueprint
   A. Window Form Factor & Morphing Splash
   - Frameless Splash Startup: Double-clicking pulsar.exe instantly displays a $340 \times 180\text{ px}$ frameless splash loader in under 100ms to mask local token verification and file checks.
   - Smooth Resize: Once verified, the window smoothly expands to the main dashboard ($840 \times 520\text{ px}$) without window flickers.Minimalist Aesthetics: OLED Black (#000000) and Industrial Zinc (#09090b) dark
     modes designed to match modern PC desk setups.B. Centered Primary CTA & Version SwitcherThe main dashboard features a central focal point that minimizes user cursor movement:
     
```text
┌────────────────────────────────────────────────────────────────────────┐
│ ✦ PULSAR CLIENT                                        ─  □  ✕  │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│                        P U L S A R   C L I E N T                       │
│                 O P E N - S O U R C E   E N G I N E                    │
│                                                                        │
│                    ┌──────────────────────────────┐                    │
│                    │     ▶   P L A Y   G A M E    │                    │ <- Centered Hero CTA
│                    └──────────────────────────────┘                    │
│                    [ ⚡ 1.20.4 • Fabric v0.15.7 ▾ ]                     │ <- Version Switcher
│                                                                        │
│             [●] Sodium • Lithium • FerriteCore Active                  │
│                                                                        │
├────────────────────────────────────────────────────────────────────────┤
│  [●] Steve (Online)                                  ⚡ 4.0 GB / 16 GB │ <- Minimal Ribbon
└────────────────────────────────────────────────────────────────────────┘
```
- Center Hero Action: Prominent PLAY GAME button situated directly in the middle of the screen.Dropdown Version Switcher: [ ⚡ 1.20.4 • Fabric v0.15.7 ▾ ] positioned immediately below the PLAY button for quick profile swapping (e.g., Fabric 1.20.4, OptiFine 1.16.5).C.
- Auto-Hiding Left Hover SidebarPinned to the left edge; remains hidden during normal view and smoothly expands on cursor proximity.Navigation includes: Dashboard, Library (installed mods/packs), Mods (Modrinth browser integration), Settings, and Connect Discord RPC.D.
- Utility Quick Toolbar & Status BadgesLucide icons for status badges (Sodium Active, 0% Telemetry, Fabric 1.20.4).Quick toolbar for opening .minecraft folder (FolderOpen), toggling live JVM execution output (Terminal), checking GitHub repo (Github), and syncing core assets (RefreshCw).5.
- Authentication & Process LifecycleMicrosoft OAuth 2.0 PKCE: Clicking "Sign in with Microsoft" spins up a temporary Rust HTTP loopback server on 127.0.0.1 and opens the official Microsoft authentication URL in the user's default browser.Local Token Storage: Rust catches the OAuth code, exchanges it for mc_token, encrypts it locally, and closes the loopback server.JVM Process Spawning: Builds high-performance G1GC JVM execution arguments, attaches Fabric Loader, spawns Java detached from the launcher, and automatically minimizes or terminates the launcher process to free system memory.6.

## In-Game Engine & Discord RPCPlaintext
- [Discord Overlay]  ──> Injects Graphics DLL Hooks ──> Render Conflicts (Sodium) ──> FPS Drops
- [Discord RPC]      ──> Local Unix/Pipe Socket     ──> Lightweight JSON Payload  ──> 0 FPS Loss
- 
---

Zero Discord Overlay: DLL injection hooks are omitted to eliminate OpenGL driver crashes and micro-stutters with Sodium/NVIDIUM.Discord Rich Presence: Connects via local pipe socket (\\.\pipe\discord-ipc-0) for zero FPS performance impact.Native Mixin Main Menu: Custom main menu built using Fabric Mixins targeting TitleScreen.class natively (avoiding MCEF/Chromium embedded framework RAM bloat).7. Phase 1 Development Scope (Alpha Core)[x] Finalize project scope, name (Pulsar Client), and architectural blueprint.[ ] Implement Rust/Tauri Microsoft OAuth 2.0 loopback authentication engine.[ ] Build Tokio parallel downloader for Mojang assets and Fabric Loader runtime.[ ] Build React/Shadcn UI (Frameless Splash, Centered PLAY CTA, Interactive Version Switcher, Auto-Hiding Sidebar).[ ] Configure JVM process spawning with pre-tuned G1GC optimization flags.[ ] Bundle core optimization mods (Sodium, Lithium, FerriteCore, ModernFix, Iris, NVIDIUM).[ ] Deploy automated GitHub Actions CI/CD pipeline for public release binaries.8. Security & Code Signing PolicyNo Certificate Tax: Rejecting $200–$300/year Microsoft Code Signing certificate fees to remain 100% community-funded and open.Radical Transparency:Automated public release builds produced by GitHub Actions CI/CD.Public SHA-256 Checksums published for every release binary.Visual 2-step setup instructions ("More Info" $\rightarrow$ "Run Anyway") on the download page.
