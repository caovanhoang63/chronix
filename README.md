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

## 🤝 Contributing

Currently not accepting PRs as the core architecture is rapidly changing.

## 📜 License

This project is licensed under the **GNU GPLv3 License** - see the [LICENSE](LICENSE) file for details.

-----

\<div align="center"\>
\<p\>If you find Chronix useful, consider buying me a coffee to keep the caffeine flowing\!\</p\>
\<a href="https://www.google.com/search?q=https://ko-fi.com/username"\>
\<img src="https://www.google.com/search?q=https://img.shields.io/badge/Ko--fi-F16063%3Fstyle%3Dfor-the-badge%26logo%3Dko-fi%26logoColor%3Dwhite" alt="Support on Ko-fi"\>
\</a\>
\</div\>

```
```