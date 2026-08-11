# generators/terminal-card

Interactive **SVG Terminal Card Generator** — a single self-contained `index.html` for designing retro-terminal-style cards with console output, an optional Git-style timeline rail, an animated cursor, decorative sparkles, configurable themes, and GitHub-oriented export workflows.

**[→ Open the live generator](https://dvrdnz.github.io/asset-forge/generators/terminal-card/index.html)**

> **Current UI version: v2.0**

No build step, no package installation, no server, and no runtime CDN dependencies are required. The editor runs entirely in the browser. The only optional network request is the GitHub API request used to trigger a configured Actions workflow.

---

## Feature Overview

### 1. Advanced Live Editor

* **3-tab workflow:** Dedicated interfaces for **Terminal**, **Timeline Rail**, and **Themes**.
* **Content templates:** 5 built-in presets to jumpstart a design.
* **Local persistence:** card configuration is automatically saved to `localStorage` and survives reloads. The GitHub token is never persisted.
* **Live preview tools:** switch between **Preview** and **Code**, zoom in/out, reset zoom, inspect rendered dimensions and SVG size, and compare preview backgrounds.
* **Light/dark editor UI:** the generator chrome can switch independently of the exported SVG color theme.
* **Context-aware canvas controls:** width, height, presets, and quick visibility toggles are grouped into the Themes/canvas workflow.

### 2. Terminal Customization

* **Window chrome:** Toggle between macOS-style traffic lights or minimal GitHub-style gray dots.
* **Dynamic console lines:** Add, remove, and reorder up to **20** console lines with prefix presets and individual color selection.
* **Per-line styling:** Configure prefix, text, and color for each terminal line.
* **Blinking cursor:** Native SMIL-animated cursor with configurable symbol, color, and blink cycle; the animation continues to work when the SVG is embedded in a README.
* **Window title controls:** Customize title text and title color.
* **Terminal Y positioning:** Adjust the vertical placement of the terminal inside the SVG canvas with live preview and clamped input.
* **Terminal font size:** Adjustable from **9–16 px**.
* **Font presets:**
  - Consolas / SFMono
  - GitHub Monospace
  - Menlo / Monaco (macOS)
  - Lucida Console (Windows)
  - Courier New
* **Input normalization:** configuration values are normalized and text fields are length-limited before rendering.

### 3. Timeline Rail System

* **Vertical progression:** A Git-style rail connecting project milestones.
* **Active node:** Highlight a current mission with title, status, iconography, and theme-aware accent colors.
* **History nodes:** Add up to **12** past milestones with adjustable opacity and specialized icons, including a lock icon.
* **Dynamic sizing:** the minimum canvas height is recalculated from the actual terminal/timeline content.

### 4. Themes & Visual Engine

* **6 built-in SVG themes:** GitHub Dark, GitHub Light, Dracula, Nord, Tokyo Night, and Cyberpunk.
* **Theme-managed colors:** Theme changes can automatically update terminal border, cursor, sparkle, active-node, and window-title colors.
* **Manual color overrides:** Editing a theme-bound color disables that individual binding so later theme switches do not overwrite the manual choice.
* **Decorative sparkles:** Enable/disable corner sparkles and configure their color and opacity.
* **Canvas background:** Use a transparent SVG canvas or a custom solid canvas background color.
* **Preview background comparison:** transparent grid, GitHub Dark, and GitHub Light.

### 5. Canvas Sizing

The editor provides explicit width and height controls plus presets:

| Preset | Size |
| --- | ---: |
| GitHub Card | 460 × 650 |
| Square | 800 × 800 |
| Banner | 1024 × 512 |
| Open Graph | 1200 × 630 |
| Reset | 460 × 650 |

The configurable canvas is bounded to:

* **Minimum width:** 365 px
* **Maximum width:** 1400 px
* **Maximum height:** 2200 px

The selected width and height define the root SVG dimensions. The generator calculates the **minimum required height** from the current content and automatically raises the configured height when necessary; it does not automatically expand the canvas beyond the selected width or height for content width.

The minimum-height calculation accounts for terminal rows, cursor rows, timeline nodes, footer, and sparkles where applicable.

### 6. Preview and Inspection

The preview panel provides:

* **Preview / Code** view switching.
* Zoom controls from **30% to 250%**.
* Zoom reset.
* Touch pinch-to-zoom support.
* Rendered dimensions and generated SVG size.
* Transparent, GitHub Dark, and GitHub Light preview backgrounds.
* One-click raw SVG copying.
* Temporary per-button copy feedback plus the global copy notification.

### 7. Export Options

The generator can:

* Download `terminal-card.svg`.
* Copy the raw SVG source.
* Copy a README-ready HTML `<img>` snippet.
* Export the current configuration as `terminal-card-config.json`.
* Trigger a GitHub Actions `workflow_dispatch` request for automated SVG updates.

Example README embedding:

```html
<img src="assets/terminal-card.svg" width="460" alt="Terminal Card" />
```

For GitHub README use, the exported SVG uses native SVG/SMIL animation rather than JavaScript. This allows the animated cursor to continue working when the SVG is embedded as an image.

---

## Quick Start

1. Open the live generator or download `index.html`.
2. Select a content template or start from the default configuration.
3. Customize the **Terminal**, **Timeline Rail**, and **Themes** tabs.
4. Use the preview controls to inspect the result.
5. Click **Export & Copy**.
6. Either download the SVG, copy its source, or use the generated README `<img>` snippet.

Because the application is self-contained, `index.html` can be opened directly in a modern browser.

---

## Integration & Deployment

### Method 1: Manual SVG integration

1. Design the card in the generator.
2. Select **Export & Copy**.
3. Download `terminal-card.svg` or copy the raw SVG code.
4. Commit the SVG into your repository, for example:

   ```text
   assets/terminal-card.svg
   ```

5. Embed it in your README:

   ```html
   <img src="assets/terminal-card.svg" width="460" alt="Terminal Card" />
   ```

### Method 2: Automated GitHub Actions update

The generator can trigger an existing GitHub Actions workflow through `workflow_dispatch`. The browser authenticates the API request with the supplied fine-grained personal access token; the workflow runner performs the actual repository write using its `GITHUB_TOKEN`.

The target repository must already contain a suitable workflow, for example:

```text
.github/workflows/update-svg.yml
```

The workflow must accept the inputs used by the generator:

* `svg_content`
* `target_path`
* `commit_message`

The default target path shown by the UI is:

```text
assets/generated/terminal-card.svg
```

#### Fork vs. another repository

* **Forked `asset-forge`:** if you keep generated assets in the fork, make sure GitHub Actions is enabled for the fork before using the workflow.
* **Different target repository:** copy the workflow into `.github/workflows/update-svg.yml` in that repository and push it to the default branch before trying to trigger it remotely.

The generator validates the repository name, branch, workflow filename, target path, and commit message before dispatching the request. The browser-side target-path validation restricts the path to:

```text
assets/generated/<name>.svg
```

This prevents the browser-triggered workflow from being used to address arbitrary repository paths through the generator UI. The workflow itself should validate its inputs again server-side before writing files.

#### Recommended token

Use a **fine-grained GitHub personal access token** with:

* Repository access restricted to the target repository.
* **Actions: Read and write** permission.
* No browser-side repository Contents write permission.
* A short expiration period.

The token lives only in JavaScript memory and is cleared when the export modal closes and after the request lifecycle completes. It is never stored in `localStorage`.

#### Workflow dispatch size warning

The SVG is UTF-8/base64 encoded before it is sent as a `workflow_dispatch` input. The generator warns when the encoded payload exceeds 60,000 characters because GitHub's effective workflow-dispatch input size has practical limits. The warning is deliberately presented as a precaution rather than as an official GitHub limit.

---

## Persistence and Configuration

Card configuration is automatically saved to `localStorage` with a short debounce.

Persisted card state includes the generator configuration but **not the GitHub personal access token**.

The editor UI theme is stored separately under its own local-storage key.

Use the **Reset** action to restore the default card configuration and remove the persisted card state.

The v2 configuration normalizer supplies defaults for newly introduced fields such as `terminalFontSize` and `showFooter`, so older saved configurations can continue through the existing normalization path.

---

## Architecture & Design Decisions

### Why one HTML file, no build step

The application ships as a single `index.html` that can be opened directly. The file contains the UI, precompiled Tailwind CSS, JavaScript editor logic, SVG generation, icon definitions, and export functionality.

The runtime does not require a package manager, framework bundle, CDN script, external font, or separate JavaScript file. The only optional outbound request is the GitHub API request used for the Actions workflow.

### State and rendering: one config object, one render path

A single mutable `config` object holds the card definition. Configuration updates feed the editor and preview rendering paths rather than maintaining a component tree or virtual DOM.

Interactive controls use delegated `data-onclick`, `data-oninput`, and `data-onchange` handlers, so dynamically rendered terminal lines and timeline nodes do not need individual event-listener registration.

Preview rendering is scheduled through `requestAnimationFrame` for frequent updates. Window resize handling also uses `requestAnimationFrame` to avoid redundant layout work.

### Two runtimes, one artifact

The editor page and the exported SVG are deliberately separate runtimes.

* **The cursor blinks via SVG SMIL, not JavaScript.** Once exported and embedded with `<img src="terminal-card.svg">`, the SVG cannot rely on page JavaScript, so the animation remains inside the SVG itself.
* **User-controlled values are escaped before SVG interpolation.** String content is XML-escaped and numeric SVG values are validated/clamped before being used in SVG attributes.
* **Hard limits live in the configuration layer.** Terminal lines, history nodes, text fields, canvas dimensions, and other numeric configuration values are bounded before rendering rather than relying only on UI controls.

### Why no framework

The editor is a deliberately small form-driven application. Vanilla JavaScript plus delegated event handling keeps the single-file architecture understandable and avoids a runtime framework or build pipeline.

### Trade-off: security vs. maintainability

The CSP pins the inline script and style blocks to SHA-256 hashes. Any edit to those blocks changes their hashes and therefore requires the CSP declaration to be updated as part of the same change.

---

## Security Notes

### GitHub token handling

GitHub PATs are:

* Entered only when the GitHub Actions export is used.
* Stored in a transient JavaScript variable.
* Never persisted to `localStorage`.
* Used to authenticate the request to `api.github.com`.
* Cleared when the export modal closes and after the request lifecycle completes.

A fine-grained PAT restricted to the target repository with **Actions: Read and write** is recommended. Avoid broad classic PATs with unnecessary permissions.

Because the token exists in browser memory while the feature is being used, normal browser/session security still matters.

### Content Security Policy

The page uses a restrictive CSP with:

* `default-src 'none'`
* hash-pinned inline script/style execution
* `connect-src https://api.github.com`
* no external images
* no external fonts
* no media
* no frames
* no objects
* no workers
* no manifests
* no form actions
* no ancestor framing

Changing the inline script or style blocks requires regenerating their corresponding SHA-256 hashes.

### SVG input handling

User-provided text is XML-escaped before it is placed into the generated SVG. Numeric configuration is normalized and bounded to prevent invalid values from propagating into SVG dimensions or attributes.

---

## Accessibility & Interaction

The editor includes several accessibility-oriented behaviors:

* WAI-ARIA dialog semantics for modals.
* Initial focus placement when a modal opens.
* `Escape` closes active modals.
* `Tab` and `Shift+Tab` are trapped within an open modal.
* Focus returns to the element that opened the modal.
* Visible focus styles for interactive controls.
* Accessible labels for interactive controls, including generated color controls.
* Reduced-motion support through `prefers-reduced-motion`.
* `noscript` fallback explaining that JavaScript is required.

The v2 modal implementation specifically addresses the previous keyboard-navigation limitation by managing focus within the active dialog.

---

## Troubleshooting & Notes

### GitHub Actions request fails

Check:

1. The workflow already exists in the target repository's default branch.
2. The workflow filename matches the value entered in the generator.
3. The selected branch exists.
4. The PAT is fine-grained and has **Actions: Read and write** access to the target repository.
5. The target path matches `assets/generated/<name>.svg`.
6. The SVG payload is not unusually large.
7. The workflow is configured with a `workflow_dispatch` trigger and accepts the expected inputs.

### SVG is larger than expected

The **minimum required canvas height** increases with the number of rendered terminal rows and timeline nodes, and can also be affected by cursor, footer, and sparkle visibility. The editor automatically raises the configured height when it would otherwise be too small, subject to the 2200 px maximum.

The configured canvas width is not automatically expanded to accommodate content.

### Theme changes do not affect a manually chosen color

This is intentional. When a theme-managed color is changed manually, its binding is disabled so later theme switches do not overwrite the custom value.

### JavaScript is disabled

The generator requires JavaScript for editing and rendering. The page displays a `noscript` message instead of pretending to be functional without JavaScript.

---

## Project Structure

```text
asset-forge/
└── generators/
    └── terminal-card/
        ├── index.html
        ├── README.md
        └── CHANGELOG.md
```

Sample generated assets are available under the repository's `examples/` directory.

---

## Browser Support

Use a modern evergreen browser with support for:

* ES6+ JavaScript
* `localStorage`
* `requestAnimationFrame`
* `backdrop-filter`
* CSS `dvh`
* native SVG SMIL animation
* Clipboard APIs where available

A fallback clipboard path using `document.execCommand('copy')` is retained for environments where the modern Clipboard API is unavailable.

---

## License

Distributed under the **MIT License**. Part of the [`asset-forge`](https://github.com/dvrdnz/asset-forge) toolkit. See the license at the root of the repository.
