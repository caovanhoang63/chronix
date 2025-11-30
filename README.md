<div align="center">
  <img src="https://via.placeholder.com/150/06b6d4/ffffff?text=Chronix+Logo" alt="Chronix Logo" width="120" height="120">
  
  # CHRONIX
  
  **AI-Powered Time Tracking for the Modern Linux Workflow**
  
  [![Go Version](https://img.shields.io/github/go-mod/go-version/username/chronix)](https://golang.org)
  [![Wails](https://img.shields.io/badge/Built%20With-Wails-red)](https://wails.io)
  [![Svelte](https://img.shields.io/badge/Frontend-Svelte-orange)](https://svelte.dev)
  [![License](https://img.shields.io/github/license/username/chronix)](LICENSE)

  <p>
    <a href="#features">Features</a> •
    <a href="#tech-stack">Tech Stack</a> •
    <a href="#installation">Installation</a> •
    <a href="#privacy">Privacy</a> •
    <a href="#contributing">Contributing</a>
  </p>
</div>

---

## ⚡ What is Chronix?

**Chronix** is a privacy-first, cross-platform time tracker designed specifically for developers and power users who demand aesthetics without compromising performance.

While macOS has *Rize* or *Screen Studio*, Linux users have long been stuck with clunky web apps or dry CLI tools. **Chronix bridges that gap.** It runs natively, tracks your active windows locally, uses lightweight AI to categorize your work, and helps you maintain your flow state with built-in blocking tools.

> **Philosophy:** Your time data is sensitive. Chronix stores everything in a local SQLite database. No cloud. No telemetry. No monthly subscriptions required to access your own data.

## 🚀 Key Features

* **🕵️ Automatic Window Tracking:**
    * Detects active window titles and application names.
    * **Wayland Support:** Native support for Hyprland (via IPC) and GNOME/KDE (via DBus).
    * **X11 Support:** Fallback support using `xgb`.
* **🧠 Local AI Classification:**
    * Stop manually tagging `github.com` as "Work". Chronix uses a lightweight local classification engine to auto-tag your activities (e.g., Neovim -> Coding, YouTube -> Entertainment).
* **🍅 Integrated Focus Mode:**
    * Built-in Pomodoro timer.
    * **Distraction Blocker:** Temporarily blocks access to time-wasting websites (via hosts file or proxy) during focus sessions.
* **📊 Deep Analytics:**
    * Visualize your day with beautiful interactive charts.
    * Track context switching, deep work ratios, and most-used apps.
* **🎨 Cyberpunk Aesthetics:**
    * Modern, dark-themed UI built with Tailwind CSS, fitting perfectly into your rice.

## 🛠️ Tech Stack

Chronix is built for performance and maintainability.

| Component | Technology | Why? |
| :--- | :--- | :--- |
| **Core Framework** | **Wails** (Go) | Native binary, lightweight memory footprint (uses WebKit), no Electron bloat. |
| **Frontend** | **Svelte** + **Vite** | Blazing fast, minimal boilerplate, truly reactive. |
| **Styling** | **TailwindCSS** + **DaisyUI** | Rapid UI development with semantic component classes. |
| **Database** | **SQLite** | Local, serverless, robust data storage. |
| **Data Access** | **sqlc** | Type-safe SQL generation. No slow ORM magic, just raw speed. |
| **Linux Native** | `xgb` / `dbus` / `IPC` | Direct system calls for X11 and Wayland integration. |

## 📥 Installation

### Option 1: Pre-compiled Binary (Recommended)
Save time and support development by grabbing the ready-to-run AppImage or `.deb`/`.rpm` file.
<a href="https://gumroad.com/your-link">
  <img src="https://img.shields.io/badge/Get%20it%20on-Gumroad-ff69b4?style=for-the-badge&logo=gumroad" alt="Buy on Gumroad">
</a>

### Option 2: Install from AUR (Arch Linux)
```bash
yay -S chronix-git
````

### Option 3: Build from Source

Chronix is 100% Open Source. You can build it yourself for free.

**Prerequisites:**

* Go 1.21+
* Node.js 18+
* `libgtk-3-dev` and `libwebkit2gtk-4.0-dev` (Linux)

<!-- end list -->

```bash
# 1. Clone the repo
git clone [https://github.com/username/chronix.git](https://github.com/username/chronix.git)
cd chronix

# 2. Install frontend dependencies
cd frontend && npm install && cd ..

# 3. Build the binary
wails build
```

## 🔒 Privacy Manifesto

We take "Privacy-First" seriously:

1.  **Local Storage:** All data lives in `~/.local/share/chronix/chronix.db`.
2.  **No Uploads:** We do not send your window titles or activity logs to any server.
3.  **Local AI:** Text classification happens on your CPU, not via OpenAI API.

## 🏗️ Architecture

Chronix is built on a **native desktop framework** (Wails) with an embedded Go backend and SvelteKit frontend. All data is stored locally in SQLite—no cloud required.

```
┌─────────────────────────────────────────┐
│   WebKit Browser (SvelteKit Frontend)   │
├─────────────────────────────────────────┤
│     ↔ RPC over WebSocket                │
├─────────────────────────────────────────┤
│   Go Backend (Wails Runtime)            │
│   - Window Tracking (X11/Wayland)      │
│   - Activity Classification             │
│   - SQLite Database                     │
└─────────────────────────────────────────┘
```

For detailed architecture, see [docs/system-architecture.md](docs/system-architecture.md).

## 🛠️ Development

### Prerequisites

* **Go 1.23+** ([download](https://golang.org/dl))
* **Node.js 18+** ([download](https://nodejs.org))
* **pnpm** (strict package manager)
  ```bash
  npm install -g pnpm
  ```
* **System libraries** (Linux)
  ```bash
  # Ubuntu/Debian
  sudo apt-get install libgtk-3-dev libwebkit2gtk-4.1-dev

  # Fedora
  sudo dnf install gtk3-devel webkit2gtk4.0-devel

  # Arch
  sudo pacman -S gtk3 webkit2gtk
  ```

### Quick Start

```bash
# Clone and navigate
git clone https://github.com/username/chronix.git
cd chronix

# Install frontend dependencies
cd frontend && pnpm install && cd ..

# Start development (auto-rebuilds on changes)
wails dev
```

The app opens in a native window with hot module replacement (HMR) for frontend changes.

### Common Development Tasks

**Type-check TypeScript**:
```bash
cd frontend && pnpm run check
```

**Format code**:
```bash
cd frontend && pnpm run format
```

**Run tests**:
```bash
cd frontend && pnpm run test
go test ./...  # Go tests
```

**Build production binary**:
```bash
cd frontend && pnpm run build
wails build -tags webkit2_41
# Output: build/bin/chronix
```

### Project Structure

See [docs/codebase-summary.md](docs/codebase-summary.md) for detailed file organization.

**Key directories**:
- `main.go` / `app.go` - Go backend entry point and RPC methods
- `frontend/src/routes/` - SvelteKit file-based routing
- `frontend/src/lib/` - Reusable components and utilities
- `docs/` - Technical documentation

### Code Standards

All code must follow [docs/code-standards.md](docs/code-standards.md):
- **Go**: Exported methods for RPC, strict error handling
- **TypeScript**: Strict mode enforced, no `any` types
- **Svelte**: Component props typed with interfaces
- **Styling**: TailwindCSS + DaisyUI (no custom CSS unless necessary)

### Frontend-Backend Integration

Go methods are automatically exposed to frontend as TypeScript RPC stubs:

```go
// Go backend (app.go)
func (a *App) Greet(name string) string {
    return fmt.Sprintf("Hello %s!", name)
}
```

```ts
// TypeScript frontend
import { Greet } from '$lib/wailsjs/wailsjs/go/main/App';

const result = await Greet("World");  // "Hello World!"
```

**Important**: Only exported (PascalCase) Go methods are callable from frontend. Bindings are auto-generated—never edit them manually.

## 🤝 Contributing

We're actively developing Chronix! Contributions are welcome once the MVP is stabilized.

**For now**:
- **Bug reports**: Open an issue on GitHub
- **Feature requests**: Discuss in issues (we're planning Q1 2025 features)
- **Code contributions**: Discussions encouraged, PRs accepted after core API stabilizes

**Before implementing a feature**:
1. Check [docs/project-overview-pdr.md](docs/project-overview-pdr.md) for roadmap
2. Open a discussion issue
3. Follow [docs/code-standards.md](docs/code-standards.md)
4. Ensure tests pass: `pnpm test && go test ./...`

## 📚 Documentation

- **[Project Overview & PDR](docs/project-overview-pdr.md)**: Vision, features, requirements
- **[Codebase Summary](docs/codebase-summary.md)**: File structure and organization
- **[Code Standards](docs/code-standards.md)**: Conventions and best practices
- **[System Architecture](docs/system-architecture.md)**: Technical design and flows

## 📜 License

This project is licensed under the **GNU GPLv3 License** - see the [LICENSE](LICENSE) file for details.

---
<div align="center">
    <p>If you find Chronix useful, consider buying me a coffee to keep the caffeine flowing !</p>
    <a href="https://www.google.com/search?q=https://ko-fi.com/username"></a>
    <img src="https://www.google.com/search?q=https://img.shields.io/badge/Ko--fi-F16063%3Fstyle%3Dfor-the-badge%26logo%3Dko-fi%26logoColor%3Dwhite" alt="Support on Ko-fi"/>
</div>

