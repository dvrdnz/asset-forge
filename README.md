# ⚒️ asset-forge

> An open-source collection of browser-based generators and copy-paste templates for GitHub assets. No build step, no package manager — CDN-loaded where needed. Open it locally or via GitHub Pages, customize your asset, and get the output.

[![GitHub Pages](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-2563eb?style=flat-square\&logo=github)](https://dvrdnz.github.io/asset-forge/)
[![License: MIT](https://img.shields.io/badge/License-MIT-7ee787?style=flat-square)](LICENSE)

---

## ✨ What is this?

`asset-forge` is a small toolbox for people who want polished, dynamic assets in their GitHub Markdowns

Currently two kinds of tools live here:

* **Generators** — interactive editors with live preview. You configure, it renders.
* **Templates** — static starting points you copy into your own repo and adapt.

---

## 🛠️ Generators

Interactive tools — open, customize, copy the output.

| Generator                                    | Description                                                     | Live                                                                               |
| -------------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| [Terminal Card](./generators/terminal-card/) | Terminal Card & Timeline Rail SVG customizer for GitHub READMEs | [Open →](https://dvrdnz.github.io/asset-forge/generators/terminal-card/index.html) |

---

## 📄 Templates

Copy-paste assets — drop into your repo and adjust.

| Template                            | Description                                                                             | Live                                               |
| ------------------------------------ | -------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| [docs-site](./templates/docs-site/) | GitHub Pages doc site with sidebar, dark/light mode, and multi-file Markdown navigation.| [This site](https://dvrdnz.github.io/asset-forge/) |

---

**GitHub Pages**

Live tools and templates are hosted at `https://dvrdnz.github.io/asset-forge/`. Fork the repo, enable Pages for your fork (Settings → Pages → Deploy from branch), and your own copy is live at `https://<your-username>.github.io/asset-forge/`.

---

## 📁 Structure

```
asset-forge/
├── .github/
│   └── workflows/
│       └── update-svg.yml
├── .gitignore
├── index.html                      ← Renders this README as GitHub Pages entry point
├── README.md
├── generators/
│   └── terminal-card/
│       ├── index.html              ← SVG Terminal Card Generator v1.0
│       └── README.md
├── templates/
│   └── docs-site/
│       └── index.html              ← Markdown doc site template
│       └── README.md
└── assets/                         ← Gitignored
    ├── generated/                  ← Local output from the "Update via GitHub Action" workflow
    └── examples/                   ← Example SVG outputs
```

---

## 🤝 Contributing

Issues and PRs are welcome — new generators, new templates, or fixes to existing ones. Since there's no build step, a contribution is typically just a self-contained `index.html` (plus CDN references where needed) under `generators/<name>/` or `templates/<name>/`.

## 📄 License

MIT
