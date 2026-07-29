# generators/terminal-card

Interactive **SVG Terminal Card Generator** — a single self-contained `index.html` that lets you design a retro-terminal-style card with command lines, a Git-style timeline rail, animated cursor, sparkles, and theme controls, then export it as scalable SVG for GitHub profile READMEs.

**[→ Open the live generator](https://dvrdnz.github.io/asset-forge/generators/terminal-card/index.html)**

No build step, no dependencies, no server required. Everything runs client-side in the browser.

---

## Feature Overview

### 1. Advanced Live Editor

* **3-tab workflow:** Dedicated interfaces for **Terminal** (window + lines), **Timeline Rail** (milestones), and **Themes** (appearance + fonts).
* **Content templates:** 5 built-in presets to jumpstart your design.
* **Local persistence:** your configuration auto-saves to `localStorage` between visits, so it survives a reload. The GitHub token never does — see [Security Notes](#security-notes).
* **Preview tools:** switch between **Preview** and **Code**, zoom in/out, reset zoom, and toggle preview backgrounds for easier editing.

### 2. Terminal Customization

* **Window chrome:** Toggle between macOS-style traffic lights or minimal GitHub-style gray dots.
* **Dynamic console lines:** Add, remove, and reorder up to 20 console lines with prefix presets (`➜`, `»`, `$`, `✔`, `⚡`) and individual color selection.
* **Blinking cursor:** Native SMIL-animated cursor (works without JS in the README) with configurable symbols, colors, and blink cycle duration.
* **Window title controls:** Customize title text and title color.

### 3. Timeline Rail System

* **Vertical progression:** A sleek Git-style rail connecting project milestones.
* **Active node:** Highlight current missions with glowing accents and status metadata.
* **History nodes:** Add up to 12 past milestones with adjustable opacity and specialized icons, including a lock icon for private projects.

### 4. Themes & Visual Engine

* **6 built-in themes:** GitHub Dark, GitHub Light, Dracula, Nord, Tokyo Night, and Cyberpunk.
* **Modern typography:** Support for local monospace fonts such as Fira Code, JetBrains Mono, Consolas / SFMono, and Courier New with system fallbacks.
* **Decorative accents:** SMIL-animated corner sparkles with configurable opacity and color.
* **Canvas control:** Toggle transparent backgrounds for seamless GitHub UI integration.

### 5. Export Options

* **Direct download** of `terminal-card.svg`.
* **Copy raw SVG code** for manual pasting into a repo file.
* **Copy an HTML `<img>` tag** for a README-friendly embed snippet.
* **Export JSON configuration** for sharing or versioning a setup.
* **Push straight to a GitHub repo** by triggering a `workflow_dispatch` on an Actions workflow.

---

## Quick Start

1. Download or clone this generator's `index.html`.
2. Open it directly in a browser.
3. Pick a content template or start from the default, tweak it in the **Terminal** / **Timeline Rail** / **Themes** tabs, and watch the preview update live.
4. Use **Export & Copy** when you are happy with the result.

---

## Integration & Deployment

### Method 1: Manual (Quickest)

1. Design your card in the generator.
2. Click **Export & Copy** → **Copy raw SVG code**.
3. In your GitHub repository, create a new file such as `assets/terminal-card.svg`, paste the code, and commit.
4. Link it in your `README.md`:

```markdown
<img src="assets/terminal-card.svg" width="460" alt="Terminal Card" />
```

### Method 2: Automated Update (GitHub Actions)

Instead of downloading the SVG and committing it by hand, the generator can trigger a GitHub Actions workflow that writes and commits it for you.

1. Get the workflow file into your target repo's default branch. Two cases:

   * **You forked asset-forge** and plan to collect your generated assets in this repo: the workflow already exists at `.github/workflows/update-svg.yml`. GitHub disables Actions on forks by default, so open the **Actions** tab of your fork once and enable workflows there.
   * **Your target is a different repo** (for example your profile README repo): copy `update-svg.yml` from this repo into `.github/workflows/update-svg.yml` there and push it to the default branch. The workflow must already exist in the repo before it can be triggered remotely.
2. Create a **fine-grained personal access token**:

   * GitHub → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**
   * **Repository access**: “Only select repositories” → choose your target repo
   * **Permissions** → **Repository permissions** → **Actions**: set to **Read and write**
   * Leave **Contents** at “No access” — the workflow does not need browser-token content access
   * Set a short expiration and generate the token

> `assets/generated/` and `assets/examples/` are gitignored either way, so a PR that only touches those paths merges without them. A new file there needs `git add -f` to be committed at all, which is exactly what this workflow does for the one path it writes to.

> `target_path` is a free-text `workflow_dispatch` input, so the workflow validates it server-side before writing anything: it must match `assets/generated/<name>.svg`, with no absolute paths and no `..` traversal. Without that check, anyone able to trigger the workflow could point `target_path` at any file in the repo, including other workflow files.

---

## Architecture & Design Decisions

### Why one HTML file, no build step


Two decisions drive almost everything below: the app ships as a single `index.html` you can open by double-clicking, and the page runs under a Content-Security-Policy locked to `default-src 'self'`, with `script-src` and `style-src` pinned to the exact SHA-256 hashes of the inline `<script>` and `<style>` blocks. Pulling in a CDN, a framework, or a second local file would mean either loosening that CSP or recomputing its hashes, so the rest of the stack is chosen specifically to keep that CSP tight.

* **Single-file architecture:** The complete application—including UI, styling, rendering logic, icons, and export engine—is contained within one monolithic `index.html`.
* **Dependency isolation:** No external assets are fetched at runtime. CSS, JavaScript, SVG icon paths, and every required resource are embedded directly into the document. The only outbound network communication is the optional GitHub API request used for the Actions-based push workflow.
* **Logic:** Vanilla JavaScript (ES6+) in a single inline `<script>` block.
* **Styling:** Precompiled Tailwind CSS embedded as one inline `<style>` block.
* **Graphics:** Native SVG with SMIL animations instead of Canvas or JavaScript-driven rendering.
* **Security:** Eliminating runtime dependencies.
* **Requirements:** A modern evergreen browser supporting `backdrop-filter`, `dvh`, and SMIL.


### State and rendering: one config object, one re-render

There is no component tree or virtual DOM. A single mutable `config` object holds the entire card definition; every edit calls a setter such as `updateConfigField()` or `updateHistoryNode()`, and then `renderEditor()` redraws the editor UI while `generateSVG()` redraws the preview from scratch. It is a “throw it away and redraw” model — simple to reason about, because the state is small and the whole file is meant to be readable top to bottom.

Events are not bound per element. Three listeners — `click`, `input`, `change` — sit on `document` and read a `data-onclick` / `data-oninput` / `data-onchange` attribute off whatever was actually interacted with, then look up the matching handler in a central `ACTIONS` table. That is the point of the dispatcher: terminal lines and history nodes are added and removed at runtime, and delegation means a newly rendered line or node just works without any code having to re-bind a listener to it.

### Two runtimes, one artifact

The editor page and the exported SVG are two different execution environments, and that split explains a few choices that otherwise look unusual:

* **The cursor blinks via SMIL, not JS.** Once exported and embedded as `<img src="terminal-card.svg">` in a GitHub README, the SVG runs inside an `<img>` tag — GitHub’s renderer never executes a `<script>` there. Animation has to be native to SVG to survive the trip from editor to README.
* **Every value written into the SVG string is escaped and clamped before it is concatenated.** `escapeXML()` sanitizes every interpolated string, and `safeSvgNumber()` guards numeric attributes against `NaN` / `Infinity`, so malformed themes or config values do not produce broken or exploitable markup.
* **Hard caps live in the config layer, not just the UI.** `MAX_TERMINAL_LINES` (20), `MAX_HISTORY_NODES` (12), and `clampText()` truncation exist so the additive SVG canvas cannot grow past GitHub’s practical Markdown width even under an extreme configuration.

### Why no framework

React or Vue would need to be bundled or loaded from somewhere — and “somewhere” is exactly what the CSP above is designed not to have. The UI is form-like: elements get added, removed, and reordered, but never nested very deeply. The `ACTIONS` table plus a full re-render covers that without a build step, a bundle, or a virtual DOM that would need to stay in sync across two very different rendering targets: the live editor and the exported static SVG.

### Trade-off: security vs. maintainability

The hash-pinned CSP is the app’s main defense against injected script, but it means the source cannot be edited casually. Change so much as a character inside the inline `<script>` or `<style>` block, and its SHA-256 hash no longer matches the one declared in the `<meta http-equiv="Content-Security-Policy">` tag. Editing this file means recomputing and updating those hashes as part of the change, not as an afterthought.

---

## Security Notes

* **RAM-only tokens:** GitHub PATs are stored in transient JS variables. They are never written to `localStorage` and are nulled out immediately after use or when the modal closes. The token is only ever sent to `api.github.com` over HTTPS, and only to trigger the workflow — never to write repo contents directly. Scope tokens as narrowly as GitHub allows: a fine-grained PAT limited to **Actions: Read and write** on a single repository, with a short expiration, is the recommended setup. Avoid classic PATs with broad `repo` scope for this feature. Because the token is client-held, anyone with script-injection access to the page while it is open could read it for the duration of the session.
* **Content Security Policy:** locked down to `default-src 'self'`, with `script-src` / `style-src` restricted to SHA-256 hashes of the exact inline `<script>` / `<style>` blocks, including the `<style>` embedded inside the generated SVG itself. `connect-src` only allows `https://api.github.com`. There are no third-party scripts, fonts, or CDNs.
* **Sanitization:** all user inputs are passed through `escapeXML()` to prevent SVG-based injection attacks.

---

## Critical Troubleshooting & Notes

* **SVG canvas dimensional overflow:** while the UI enforces hard limits on terminal lines and truncates text, the SVG height and width grow additively. Extreme configurations could theoretically stretch the canvas beyond optimal GitHub Markdown bounds.
* **GitHub API rate limits:** the “Push to GitHub” feature communicates directly from your browser. Excessive usage may trigger temporary IP-based rate limiting.
* **Validation:** repository names and branch names are validated before dispatching events.
* **Modal Keyboard Focus (known limitation):** The export/push modal is marked `role="dialog" aria-modal="true"`, but focus isn't yet moved into it on open or trapped there — `Tab` can still reach controls behind the overlay while the modal is up. No effect on mouse use; matters for keyboard and screen-reader users. Tracked for a future update.

---

## Project Structure

This generator lives under `generators/terminal-card/` in the [`asset-forge`](https://github.com/dvrdnz/asset-forge) repo, alongside other GitHub-profile asset generators. See [`asset-forge/examples`](https://github.com/dvrdnz/asset-forge/tree/main/examples) for sample output.

---

## Browser Support

Requires a modern evergreen browser. The app uses `backdrop-filter`, native SVG SMIL `<animate>` for the cursor blink, and CSS `dvh` units. JavaScript is required — the page shows a `<noscript>` fallback message otherwise.

---

## License

Distributed under the MIT License. Part of the [asset-forge](https://github.com/dvrdnz/asset-forge) toolkit. See the license at the root of the `asset-forge` repository.
