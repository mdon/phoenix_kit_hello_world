# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## Project Overview

PhoenixKit plugin module template — a minimal, production-ready example for building PhoenixKit plugin modules. Implements the `PhoenixKit.Module` behaviour for auto-discovery by a parent Phoenix application.

## Common Commands

```bash
mix deps.get          # Install dependencies
mix test              # Run all tests
mix test test/phoenix_kit_hello_world_test.exs  # Run specific test file
mix test --only tag   # Run tests matching a tag
mix format            # Format code (imports Phoenix LiveView rules)
mix credo             # Static analysis / linting
mix dialyzer          # Type checking
mix docs              # Generate documentation
```

## Architecture

This is a **library** (not a standalone Phoenix app) — there is no `config/` directory, no endpoint, no router.

### Key Modules

- **`PhoenixKitHelloWorld`** (`lib/phoenix_kit_hello_world.ex`) — Main module implementing `PhoenixKit.Module` behaviour. Declares required callbacks (`module_key`, `module_name`, `enabled?`, `enable_system`, `disable_system`) and optional ones (`admin_tabs`, `permission_metadata`, `get_config`, etc.).

- **`PhoenixKitHelloWorld.Web.HelloLive`** (`lib/phoenix_kit_hello_world/web/hello_live.ex`) — LiveView page for the admin panel. PhoenixKit wraps it in the admin layout automatically.

### How It Works

1. Parent app adds this as a dependency in `mix.exs`
2. PhoenixKit scans `.beam` files at startup and auto-discovers modules (zero config)
3. `admin_tabs/0` callback registers admin pages; PhoenixKit generates routes at compile time
4. Settings are persisted via `PhoenixKit.Settings` API (DB-backed in parent app)
5. Permissions are declared via `permission_metadata/0` and checked via `Scope.has_module_access?/2`

## Critical Conventions

- **Module key** must be consistent across all callbacks: lowercase with underscores (`"hello_world"`)
- **Tab IDs**: prefixed with `:admin_` (e.g., `:admin_hello_world`)
- **URL paths**: use hyphens, not underscores (`"hello-world"`)
- **Navigation paths**: always use `PhoenixKit.Utils.Routes.path/1`, never relative paths
- **`enabled?/0`**: must rescue errors and return `false` as fallback (DB may not be available)
- **LiveViews use `PhoenixKitWeb` macros** — use `use PhoenixKitWeb, :live_view` (not `use Phoenix.LiveView` directly). This imports PhoenixKit's core components (`<.icon>`, `<.button>`, etc.), Gettext, layout config, and HTML helpers. Use PhoenixKit components for consistent admin UI.
- **JavaScript hooks**: must be inline `<script>` tags; register on `window.PhoenixKitHooks`
- **LiveView assigns** available in admin pages: `@phoenix_kit_current_scope`, `@current_locale`, `@url_path`

## Routing: Single Page vs Multi-Page

**Single admin page** (this template): The `live_view:` field on `admin_tabs/0` auto-generates the route. No route module needed.

**Multiple admin pages**: You MUST use a route module. The `live_view:` field only generates ONE route per tab — it can't handle sub-pages like `/admin/your-module/new` or `/admin/your-module/:id/edit`. Steps:

1. Uncomment the routes in `lib/phoenix_kit_hello_world/routes.ex`
2. Uncomment `route_module/0` in the main module
3. Remove the `live_view:` field from `admin_tabs/0` (the route module takes over)
4. Define ALL admin LiveView routes in both `admin_locale_routes/0` AND `admin_routes/0`

Both functions define the same routes — one for localized paths (`:locale` prefix) and one for non-localized. Every route needs a unique `:as` name (use `_localized` suffix).

**Catch-all public routes** (`/:slug`, `/:group/*path`): MUST go in `public_routes/1`, NOT `generate/1`. Routes in `generate/1` are placed early and will intercept `/admin/*` paths.

## Tailwind CSS Scanning

Modules with templates using Tailwind classes must implement `css_sources/0` returning their OTP app name as an atom list (e.g., `[:my_module]`). PhoenixKit's installer (`mix phoenix_kit.install`) discovers these and adds `@source` directives to the parent's `app.css`. Without this, Tailwind purges the module's CSS classes. Headless modules without UI can skip this.

## Versioning & Releases

### Tagging & GitHub releases

Tags use **bare version numbers** (no `v` prefix):

```bash
git tag 0.1.0
git push origin 0.1.0
```

GitHub releases are created with `gh release create` using the tag as the release name. The title format is `<version> - <date>`, and the body comes from the corresponding `CHANGELOG.md` section:

```bash
gh release create 0.1.0 \
  --title "0.1.0 - 2026-03-24" \
  --notes "$(changelog body for this version)"
```

### Full release checklist

1. Update version in `mix.exs`, `lib/phoenix_kit_hello_world.ex` (`version/0`), and the version test
2. Add changelog entry in `CHANGELOG.md`
3. Commit: `"Bump version to x.y.z"`
4. Push to main
5. Create and push git tag: `git tag x.y.z && git push origin x.y.z`
6. Create GitHub release: `gh release create x.y.z --title "x.y.z - YYYY-MM-DD" --notes "..."`

## Pull Requests

### Commit Message Rules

Start with action verbs: `Add`, `Update`, `Fix`, `Remove`, `Merge`. **NEVER mention Claude or AI assistance** in commit messages.

### PR Reviews

PR review files go in `dev_docs/pull_requests/{year}/{pr_number}-{slug}/` directory. Use `{AGENT}_REVIEW.md` naming (e.g., `CLAUDE_REVIEW.md`, `GPT_REVIEW.md`). See `dev_docs/pull_requests/README.md`.

## External Dependencies

- **PhoenixKit** (`~> 1.7`) — Module behaviour, Settings API, shared components, RepoHelper
- **Phoenix LiveView** (`~> 1.0`) — Admin LiveViews

## Two Module Types

- **Full-featured**: Admin tabs, routes, UI, settings (this template)
- **Headless**: Functions/API only, no UI — still gets auto-discovery, toggles, and permissions
