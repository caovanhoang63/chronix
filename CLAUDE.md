# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Chronix** is a privacy-first, cross-platform time tracker built with Wails (Go backend) and SvelteKit (frontend). It features AI-powered automatic window tracking, local data storage, and a cyberpunk-themed UI designed specifically for Linux developers and power users.

**Core Philosophy**: All data remains local (`~/.local/share/chronix/chronix.db`). Zero external API calls, no telemetry, no cloud dependencies.

## Build & Development Commands

### Development

```bash
# Start Wails dev server (HMR + auto-rebuild)
wails dev

# Type-check TypeScript (watch mode)
cd frontend && pnpm run check:watch

# Build frontend only
cd frontend && pnpm run build

# Format code
cd frontend && pnpm run format

# Run linting
cd frontend && pnpm run lint

# Run tests
cd frontend && pnpm run test        # Run once
cd frontend && pnpm run test:unit   # Watch mode
```

### Production Build

```bash
# Build frontend
cd frontend && pnpm install && pnpm run build

# Build Wails binary with WebKit2.4.1 tags
wails build -tags webkit2_41

# Output: build/bin/chronix (native executable)
```

### Testing

```bash
# Go tests
go test ./...

# Frontend unit tests
cd frontend && pnpm run test:unit

# TypeScript type checking
cd frontend && pnpm run check
```

## Architecture

### Wails Desktop Framework

Chronix is a **single native binary** that embeds both Go backend and SvelteKit frontend:

```
┌─────────────────────────────────────┐
│     Chronix Native Binary           │
├─────────────────────────────────────┤
│  WebKit Renderer  ◄──RPC──►  Go     │
│  (SvelteKit SPA)    (WS)  (Wails)   │
│                                     │
│  - Dashboard              - Window  │
│  - Analytics              Tracker   │
│  - Settings               - AI      │
│  - Timeline               Classifier│
│                           - DB      │
└─────────────────────────────────────┘
```

**Key Integration Points**:
- Frontend assets embedded via `//go:embed all:frontend/dist` in `main.go`
- Frontend-backend communication via WebSocket RPC
- Auto-generated TypeScript bindings from Go methods
- WebKit2 GTK renderer (not Electron)

### Frontend-Backend Communication

**Go Backend** (`app.go`):
```go
// Only EXPORTED (PascalCase) methods are callable from frontend
func (a *App) GetActiveWindow() (WindowSession, error) {
    return session, nil
}
```

**Auto-Generated TypeScript** (`src/wailsjs/wailsjs/go/main/App.d.ts`):
```ts
// AUTO-GENERATED - DO NOT EDIT
export function GetActiveWindow(): Promise<WindowSession>;
```

**Frontend Usage**:
```svelte
<script lang="ts">
    import { GetActiveWindow } from '$lib/wailsjs/wailsjs/go/main/App';

    const session = await GetActiveWindow();
</script>
```

### Directory Structure

```
chronix/
├── main.go                    # Wails entry point
├── app.go                     # App struct + RPC methods
├── wails.json                 # Wails configuration
├── go.mod                     # Go dependencies
├── frontend/
│   ├── src/
│   │   ├── routes/            # SvelteKit file-based routing
│   │   │   ├── +layout.svelte # Root layout
│   │   │   ├── +page.svelte   # Home page (/)
│   │   │   └── layout.css     # Global styles (Tailwind imports)
│   │   ├── lib/               # Reusable components
│   │   │   ├── components/    # Svelte components
│   │   │   ├── stores/        # Svelte stores (global state)
│   │   │   └── utils/         # Helper functions
│   │   └── wailsjs/           # AUTO-GENERATED Wails bindings
│   ├── package.json           # Frontend dependencies
│   ├── vite.config.ts         # Vite + Vitest config
│   ├── svelte.config.js       # SvelteKit static adapter
│   └── tsconfig.json          # TypeScript strict mode
├── docs/                      # Technical documentation
│   ├── project-overview-pdr.md
│   ├── codebase-summary.md
│   ├── code-standards.md
│   └── system-architecture.md
└── build/                     # Wails build output
```

## Code Conventions

### Go Backend

**Method Exposure to Frontend**:
- Only **PascalCase** (exported) methods are RPC-callable
- Use `camelCase` for internal/private methods
- Always return `(result, error)` pattern

```go
// ✅ RPC-callable from frontend
func (a *App) GetWindowSessions() ([]WindowSession, error) { }

// ❌ NOT callable (private)
func (a *App) trackWindow(title string) { }
```

**Error Handling**:
```go
// Always wrap errors with context
sessions, err := db.Query()
if err != nil {
    return nil, fmt.Errorf("querying sessions: %w", err)
}
```

**Type Safety**:
- Minimize use of `interface{}` or `map[string]interface{}`
- Define explicit struct types for all data structures
- Use struct tags for JSON serialization: `json:"fieldName" db:"field_name"`

### TypeScript/Svelte Frontend

**Strict Mode Enforced**:
- No `any` types allowed
- All component props must have interfaces
- Use union types over ambiguous types

```ts
// ✅ Good: Explicit types
interface Props {
    sessions: ActivitySession[];
    onSelect?: (session: ActivitySession) => void;
}

// ❌ Bad: Avoid any
const data: any = await fetchData();
```

**Svelte 5 Runes** (component patterns):
```svelte
<script lang="ts">
    // Props declaration
    interface Props {
        sessions: ActivitySession[];
    }
    let { sessions }: Props = $props();

    // Reactive state
    let selectedIndex = $state<number | null>(null);

    // Derived state
    let selectedSession = $derived(
        selectedIndex !== null ? sessions[selectedIndex] : null
    );

    // Side effects
    $effect(() => {
        console.log('Sessions updated:', sessions);
    });
</script>
```

**Store Pattern** (global state):
```ts
// lib/stores/appState.ts
import { writable } from 'svelte/store';

export const activities = writable<ActivitySession[]>([]);

// In components: use $activities to auto-subscribe
```

### Styling (TailwindCSS + DaisyUI)

**Prefer Tailwind utilities over custom CSS**:
```svelte
<!-- ✅ Good -->
<div class="flex items-center gap-4 rounded-lg bg-slate-900 p-4">

<!-- ❌ Avoid custom CSS -->
<style>
    .card { background: #1e293b; padding: 1rem; }
</style>
```

**DaisyUI Components**:
```svelte
<button class="btn btn-primary">Click</button>
<div class="card bg-base-100 shadow-lg">
    <div class="card-body">Content</div>
</div>
```

**Cyberpunk Color Palette**:
- Primary: `text-cyan-400`, `bg-cyan-500`
- Secondary: `text-purple-400`, `bg-purple-500`
- Accent: `text-orange-400`, `bg-orange-500`
- Background: `bg-slate-900`, `bg-slate-800`

## Critical Patterns

### Adding Backend Methods

1. Add exported method to `app.go`:
   ```go
   func (a *App) MyMethod(arg string) (Result, error) {
       // Implementation
       return result, nil
   }
   ```

2. Run `wails dev` or `wails generate module`

3. Auto-generated TypeScript binding appears in `src/wailsjs/`

4. Import and use in frontend:
   ```ts
   import { MyMethod } from '$lib/wailsjs/wailsjs/go/main/App';
   const result = await MyMethod("arg");
   ```

### Wails Bindings (Never Edit Manually)

- Files in `frontend/src/wailsjs/` are AUTO-GENERATED
- Regenerated on every build
- If bindings are outdated: run `wails dev` or `wails build`

### SvelteKit Routing

**File-based routing** in `src/routes/`:
- `+page.svelte` → Route component
- `+layout.svelte` → Layout wrapper
- `+page.ts` → Load data before rendering
- `+server.ts` → API endpoint (not used in SPA mode)
- `[param]/` → Dynamic routes
- `[[optional]]/` → Optional segments

### Testing Patterns

**Go Table-Driven Tests**:
```go
func TestClassification(t *testing.T) {
    tests := []struct {
        name     string
        title    string
        expected string
    }{
        {"neovim", "nvim — main.go", "Coding"},
        {"youtube", "YouTube — Watch", "Entertainment"},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := ClassifyActivity(tt.title)
            if result != tt.expected {
                t.Errorf("expected %s, got %s", tt.expected, result)
            }
        })
    }
}
```

**Svelte Component Tests** (Vitest):
```ts
import { render } from 'vitest-browser-svelte';
import TimeDisplay from './TimeDisplay.svelte';

it('renders formatted time', async () => {
    const { getByText } = render(TimeDisplay, {
        props: { time: new Date('2025-11-30T14:30:00') }
    });

    expect(getByText('2:30 PM')).toBeTruthy();
});
```

## Project State & Planned Features

### Current Status: MVP Foundation

**Implemented**:
- ✅ Wails framework integration
- ✅ SvelteKit frontend scaffolding
- ✅ Auto-generated RPC bindings
- ✅ Build pipeline (frontend → dist → embed in Go binary)
- ✅ TypeScript strict mode
- ✅ TailwindCSS + DaisyUI styling setup
- ✅ Testing infrastructure (Vitest + Playwright)

**Planned (Not Yet Implemented)**:
- ⏳ Window tracking (X11/Wayland via DBus/XGB)
- ⏳ SQLite database with sqlc
- ⏳ AI activity classification (local processing)
- ⏳ Focus mode with Pomodoro timer
- ⏳ Distraction blocking (hosts file/proxy)
- ⏳ Analytics dashboard with charts
- ⏳ Settings panel
- ⏳ Data export (CSV/JSON)

### Database Schema (Planned)

```sql
-- Main window sessions table
CREATE TABLE window_sessions (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    app_name TEXT NOT NULL,
    category TEXT NOT NULL,
    start_time DATETIME NOT NULL,
    end_time DATETIME,
    duration INTEGER,  -- seconds
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_start_time ON window_sessions(start_time);
CREATE INDEX idx_category ON window_sessions(category);
```

**Database Location**: `~/.local/share/chronix/chronix.db`

### Window Tracking Implementation Notes

**Wayland Support**:
- Hyprland: via IPC (`hyprctl activewindow`)
- GNOME/KDE: via DBus (`org.gnome.Shell.Eval`)

**X11 Support**:
- Use `github.com/BurntSushi/xgb` library
- Query active window via `_NET_ACTIVE_WINDOW` property

**Polling Strategy**:
- Default interval: 5 seconds
- Track window title, app name, timestamp
- Detect window changes and create new sessions

## Common Development Pitfalls

### Backend (Go)
- Don't ignore errors (`_, err := ...` without handling `err`)
- Don't expose methods you don't want in RPC (use lowercase for private)
- Don't use `interface{}` for structured data
- Don't forget context cancellation in goroutines

### Frontend (TypeScript/Svelte)
- Don't use `any` type (TypeScript strict mode will error)
- Don't edit files in `src/wailsjs/` (auto-generated)
- Don't mutate store values directly (use `.set()` or `.update()`)
- Don't import from `wailsjs` in lib code (creates circular deps)

### Styling
- Don't write custom CSS for things Tailwind can do
- Don't hardcode colors (use Tailwind color classes)
- Don't mix DaisyUI classes with custom styles
- Don't use `!important` (indicates design system conflict)

## Documentation

For detailed technical documentation, see:
- **docs/project-overview-pdr.md**: Product vision and requirements
- **docs/codebase-summary.md**: File structure and code examples
- **docs/code-standards.md**: Conventions and quality standards
- **docs/system-architecture.md**: Technical architecture diagrams

## Privacy & Security

**Privacy-First Design**:
1. All data stored locally in `~/.local/share/chronix/chronix.db`
2. Zero external API calls or cloud uploads
3. No telemetry or usage tracking
4. AI classification runs locally on CPU
5. File permissions: 0600 (owner read/write only)

**Input Validation**:
- All RPC method inputs must be validated
- Sanitize user input before database storage
- Use parameterized queries (sqlc) to prevent SQL injection
- Limit string lengths to prevent memory exhaustion

## Build System Notes

**WebKit2 Build Tag Required**:
- All Go builds must include `-tags webkit2_41`
- Configured in `wails.json` for both dev and production builds

**Frontend Build Pipeline**:
1. `pnpm install` → Install dependencies
2. `pnpm run build` → Build SvelteKit to `frontend/dist/`
3. `wails build` → Embed dist/ into Go binary

**Binary Size**:
- Development: ~19MB
- Production (optimized): ~25MB
- No external runtime dependencies (uses WebKit GTK)

**SvelteKit Adapter**:
- Uses `@sveltejs/adapter-static` for SPA mode
- Output: `frontend/dist/` (static HTML/CSS/JS)
- No server-side rendering (embedded in desktop app)

---

**Last Updated**: 2025-11-30
**Maintainer**: Hoang Van Cao (caovanhoang63)
