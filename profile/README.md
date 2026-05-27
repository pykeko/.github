# PyKeko

> A [Moorhen](https://github.com/moorhen-coot/Moorhen) fork that quacks like a [Coot](https://www2.mrc-lmb.cam.ac.uk/personal/pemsley/coot/).
> 
> A MacOS Tahoe desktop model building app for crystallography & cryoEM: adds NCS ghost overlays, a PyMOL command language scripting modal, MCP control surface for Claude, ligand and NCS mate jumping, Coot v0.9.x-style keyboard shortcuts/defaults, and more.
> 
> Named after the [pūkeko](https://en.wikipedia.org/wiki/Australasian_swamphen) bird, a New Zealand cousin of moorhens and coots.

---

## 📥 Install

**[→ Install on macOS](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/install-mac.md)** — one-click DMG, no XQuartz needed.

Or grab the latest release directly: **[Download PyKeko.dmg](https://github.com/pykeko/Moorhen-PyKeko/releases/latest)**.

---

## Repositories

| Repo | Purpose |
|---|---|
| [**Moorhen-PyKeko**](https://github.com/pykeko/Moorhen-PyKeko) | Moorhen fork with NCS ghosts, MCP control bridge, PyMOL command scripting, validation/peak/ligand cyclers, drag-atoms, single-water-at-crosshairs, and Coot 0.9-style keyboard shortcuts |
| [**PyKeko**](https://github.com/pykeko/PyKeko) | Electron wrapper — builds `PyKeko.app` (production DMG) and `PyKekoDev.app` (vite live-reload dev variant) |
| [**PyKekoMCP**](https://github.com/pykeko/PyKekoMCP) | MCP (Model Context Protocol) server that lets Claude drive a running PyKeko — load coords/maps, navigate, refine, screenshot |

---

## About

PyKeko is a desktop packaging of [moorhen-coot/Moorhen](https://github.com/moorhen-coot/Moorhen) (a WebAssembly port of Coot's engine), customized to behave like the pre-1.0 Coot crystallographers know — same keyboard shortcuts, defaults, and workflow primitives — and shipped as a one-click macOS app for environments where Coot 0.9's XQuartz/GLX stack is broken (notably macOS Tahoe).

Named after the [pūkeko](https://en.wikipedia.org/wiki/Australasian_swamphen), the New Zealand cousin of moorhens and coots.
