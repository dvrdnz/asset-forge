# Changelog

All notable changes to `generators/terminal-card` are documented here.

## [2.0.0] 

This release is a substantial update of the `terminal-card` generator. It changes the canvas sizing model, adds new editor controls, improves theme handling, accessibility, copy feedback, and modal behavior, and tightens the page's Content Security Policy.

### Added

- Added user-configurable **canvas width and height** controls.
- Added canvas presets:
  - GitHub Card — 460 × 650
  - Square — 800 × 800
  - Banner — 1024 × 512
  - Open Graph — 1200 × 630
  - Reset — 460 × 650
- Added a **minimum canvas width of 365 px** while retaining the existing maximum width of 1400 px and maximum height of 2200 px.
- Added **content-aware minimum canvas-height calculation** based on the rendered terminal, optional timeline, footer, and sparkles.
- Added a **Content Font Size** control with a supported range of 9–16 px.
- Added quick canvas-content controls for:
  - Show Timeline
  - Show Footer
  - Show Sparkles
- Added explicit `showFooter` configuration support so the generated footer can be disabled without reserving footer layout space.
- Added theme-managed **window title color** support, including separate GitHub Light handling.
- Added per-theme **muted colors** used by the SVG rendering.
- Added a reusable color-picker/hex-input helper with accessible labels and synchronized color controls.
- Added a dedicated **Reset Card** confirmation modal.
- Added per-control copy-success feedback for copy actions, including temporary check/“Copied!” states.
- Added a user-configurable **Terminal Y** SVG layout control with input clamping and preview integration.
- Added modal focus management:
  - focus moves into an opened modal
  - keyboard focus is trapped inside the active modal
  - `Escape` closes the modal
  - focus is restored to the element that opened the modal
- Added resize-aware preview-panel sizing so the preview recalculates its minimum dimensions when the viewport changes.

### Changed

- Changed the canvas sizing model from **content-expanded dimensions** to an **explicit user-selected root SVG size**. The selected canvas dimensions are now kept as the root SVG dimensions instead of being expanded to fit calculated content dimensions.
- Updated SVG layout calculations to place the terminal within the selected canvas and derive terminal height from the configured content font size and rendered line count.
- Updated the preview wrapper and preview panel to follow the resolved SVG width and height directly.
- Updated the generated README `<img>` embed snippet so its width follows the configured canvas width.
- Updated canvas controls and presets to enforce the new width/height limits and the calculated minimum height.
- Updated theme application so theme-managed window-title color follows the selected theme while preserving manual overrides through `themeColorBindings`.
- Updated the default window-title color from `#8b949e` to the new theme-managed defaults (`#818890` / `#696d72` for GitHub Light).
- Updated the font preset list:
  - removed Fira Code
  - removed JetBrains Mono
  - added GitHub Monospace
  - added Menlo / Monaco (macOS)
  - added Lucida Console (Windows)
  - retained Consolas / SFMono and Courier New
- Updated editor rendering to use the new reusable color-picker helper.
- Updated tab behavior to maintain `tabindex` correctly for the active tab and to keep the active editor panel associated with its selected tab.
- Updated maximum-history-node feedback from a browser `alert()` to the in-app toast notification.
- Updated reset behavior from the browser `confirm()` dialog to the dedicated reset confirmation modal.
- Updated copy handling so individual copy controls can receive their own success-state feedback instead of only showing the global toast.
- Updated color input handling so hexadecimal text values may be entered without a leading `#`; valid values are normalized before being committed.
- Updated configuration changes that affect layout to recalculate the minimum canvas height immediately.
- Updated preview rendering to use `requestAnimationFrame` scheduling for configuration-driven preview updates.
- Updated JSON export URL cleanup to revoke generated object URLs asynchronously.
- Updated Lucide icon class handling to preserve non-Luci­de classes while avoiding duplicate generated icon classes.

### Fixed

- Fixed the preview/canvas relationship so changing the selected canvas size no longer causes the root SVG to silently grow to the content's calculated width or height.
- Fixed layout-height handling when timeline, footer, sparkles, cursor visibility, or content font size changes affect the amount of rendered content.
- Fixed legacy or incomplete configuration handling for newly introduced `showFooter` and dimension fields by applying appropriate defaults and bounds during normalization.
- Fixed color-control accessibility by providing explicit labels for generated color inputs and history-node icon selectors.
- Fixed preview sizing on viewport resize by synchronizing the preview panel dimensions after resize events.

### Security

- Tightened the Content Security Policy from the v1 policy to a stricter default-deny policy:
  - changed `default-src` from `'self'` to `'none'`
  - removed general same-origin allowances from script, style, image, font, and connection policies
  - restricted `connect-src` to `https://api.github.com`
  - explicitly disabled images, fonts, media, objects, frames, child contexts, workers, manifests, base URLs, form actions, and ancestor framing
  - continued to use SHA-256 hashes for the inline script and style blocks
- The CSP hashes were regenerated for the updated inline script and styles. Any later modification of those inline blocks requires the corresponding hashes to be updated again.

### Accessibility

- Added WAI-ARIA dialog semantics and keyboard-oriented focus handling for the reset/export modals.
- Added focus trapping and focus restoration for modal interactions.
- Added accessible labels to newly generated color controls and history-node icon selectors.
- Improved tab semantics by keeping only the active editor tab in the tab sequence.
- Preserved reduced-motion behavior and extended accessible control labeling in the updated editor controls.

### Compatibility / Migration Notes

- Existing saved configurations continue through the existing configuration-normalization path.
- Missing `showFooter` values default to enabled, preserving the v1 footer behavior.
- New width/height values are normalized against the v2 canvas limits.
- Existing configurations without `terminalFontSize` receive the v2 default of 11 px.
- Existing configurations without the new theme-managed `windowTitleColor` binding receive theme-appropriate defaults.
- The existing storage key remains `terminal_card_config_v2`; v2 extends its normalized configuration rather than introducing a new storage key.
- The new fixed-canvas model is a behavioral change: content that exceeds the selected canvas is no longer allowed to enlarge the root SVG dimensions automatically.
