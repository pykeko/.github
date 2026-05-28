<p align="center">
  <img src="https://raw.githubusercontent.com/pykeko/PyKeko/main/PyKeko_avatar.png" alt="PyKeko" width="150" height="150" />
</p>

<h1 align="center">PyKeko</h1>

<p align="center">
  <em>A <a href="https://github.com/moorhen-coot/Moorhen">Moorhen</a> fork that quacks like a <a href="https://www2.mrc-lmb.cam.ac.uk/personal/pemsley/coot/">Coot</a>.</em><br/>
</p>

<p align="center">
  <a href="https://github.com/pykeko/Moorhen-PyKeko/releases/latest"><img alt="latest release" src="https://img.shields.io/github/v/release/pykeko/Moorhen-PyKeko?label=download&style=flat-square" /></a>
</p>

---

## What is PyKeko?

PyKeko is a MacOS desktop application for the interactive macromolecular visualization and model building of structures determined by X-ray crystallography and cryo-EM.

Under the hood it is [**Moorhen**](https://github.com/moorhen-coot/Moorhen), Coot's C++ engine (libcootapi, CCP4, Clipper, MMDB, GEMMI, RDKit) compiled to WebAssembly and wrapped in a TypeScript/React interface. PyKeko packages that into a self-contained, one-click `.app`/`.dmg` and tunes it to feel like **Coot 0.9.x** - with similar keyboard shortcuts, defaults, and workflow primitives — while adding features of its own: a [PyMOL](https://www.pymol.org/) command-language scripting console, command-line integration, a residue torsion editor, Claude-MCP control, and more.

**Why it exists:** Coot 0.9.x renders through XQuartz/GLX, which does not work on recent MacOS versions (**Tahoe** done broke it). PyKeko requires no XQuartz, no CCP4 install, and no compiler — download the `.dmg` and go.

> PyKeko is an independent project not affiliated with or endorsed by the Moorhen or Coot teams — but built on their extensive work (see [Credits](#credits)).

**Named for the **[pūkeko](https://en.wikipedia.org/wiki/Australasian_swamphen)** bird — the Australasian swamphen — a New Zealand cousin of coots and moorhens.**

## Install (macOS, Apple Silicon)

**[⬇ Download the latest PyKeko.dmg](https://github.com/pykeko/Moorhen-PyKeko/releases/latest)** · **[Full install guide](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/install-mac.md)**

1. Open the DMG and drag **PyKeko** to your Applications folder.
2. First launch (the build is unsigned): right-click the app → **Open**, or run
   `xattr -dr com.apple.quarantine /Applications/PyKeko.app`.

Already on an earlier version? See **[upgrading](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/install-mac.md#updating-to-a-newer-version)** — your settings and the `pykeko` command-line tool carry over.

Requires macOS 15.x (Tahoe) on Apple Silicon. Nothing else to install — Electron, the WebAssembly Coot engine, and the monomer libraries are all bundled. Fully offline except for "Fetch from PDB".

## What's new in 0.2.0

- **Command-line integration** — `pykeko model.pdb data.mtz ligand.cif` opens files (the `.cif` attaching as a *dictionary*, not spawning a new molecule); `pykeko 1crn` fetches by PDB id; `pykeko script.pml` runs a PyMOL-style script. A second launch of `pykeko` loads additional files into the running window (PyMOL `-R` style); `--new` opens a fresh one. Install the command from **Preferences → Install command-line launcher**.
- **Residue torsion editor** — right-click a residue → **Edit torsions**: rotate φ/ψ/χ angles with a **live Ramachandran plot** tracking the sliders.
- **`pykeko_remote.py`** — a PyMOL-`-R`-style Python client that drives a running PyKeko from your own scripts (load / refine / screenshot / run PyMOL commands / …).
- **Coot-style defaults** — black background, hydrogens shown when present, PyMOL as the default scripting language, and a one-time first-run hint pointing at the command-line launcher. (Updated keyboard shortcuts shown with `'m'`.)

Full notes: [pk-v0.2 release](https://github.com/pykeko/Moorhen-PyKeko/releases/latest).

## Capabilities

**Model building (inherited from Coot, via Moorhen).** Real-space refinement (single → triple → sphere → chain → all), rotamer fitting, peptide flips, mutations, add/delete of atoms and residues, terminal-residue building, jiggle fit, ligand fitting, map manipulation and contouring, geometry/validation analysis, and reading/writing PDB, mmCIF, MTZ, and CCP4/MRC maps — all locally, no server.

**Added by PyKeko on top of Moorhen:**

| Area | What you get |
|---|---|
| **Command line** | open files, fetch by PDB id, run `.pml` scripts, ligand-dictionary auto-attach, single-instance file handoff |
| **PyMOL scripting** | a PyMOL command-language translator in *Interactive Scripting* — full selection algebra, representations, colours, measurements, and settings; `.pml` files run directly |
| **Torsion editor** | backbone φ/ψ + sidechain χ sliders with a live Ramachandran plot |
| **NCS ghosts** | overlay every NCS-related chain transformed onto a chosen master chain (`g`) |
| **Cyclers** | step through validation outliers (`n`), difference-map peaks (`p`), ligands (`l`), and NCS mates (`o`) straight from the keyboard |
| **Coot ergonomics** | Coot 0.9-style shortcuts and defaults, single-water-at-crosshairs (`w`), drag-atoms-with-refinement (`d`) |
| **Automation** | drive it from Claude via [PyKekoMCP](https://github.com/pykeko/PyKekoMCP), or from Python via the `pykeko_remote.py` RPC client |

Full keyboard-shortcut table and per-feature writeups: **[Moorhen-PyKeko/README-MH.md](https://github.com/pykeko/Moorhen-PyKeko/blob/main/README-MH.md)** · PyMOL command reference: **[docs/pymol-translator.md](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/pymol-translator.md)**.

## The repositories

Brief map below — **each repo's own README goes in-depth on its internals.**

| Repo | What it is |
|---|---|
| **[Moorhen-PyKeko](https://github.com/pykeko/Moorhen-PyKeko)** | The fork of [moorhen-coot/Moorhen](https://github.com/moorhen-coot/Moorhen): the TypeScript/React UI plus the WebAssembly Coot engine, with PyKeko's added C++ bindings, in-page control bridge, PyMOL translator, and Coot-style UX. *The app's brains.* |
| **[PyKeko](https://github.com/pykeko/PyKeko)** | The Electron wrapper that turns the web app into a native macOS `.app` / `.dmg`, serves the bundle, runs the token-authenticated control server, and installs the `pykeko` command. *The shell.* |
| **[PyKekoMCP](https://github.com/pykeko/PyKekoMCP)** | A Model Context Protocol server that lets Claude drive a running PyKeko — load coordinates/maps, navigate, refine, edit, screenshot. *The Claude bridge.* |
| **[.github](https://github.com/pykeko/.github)** | Org-level files: this profile page and the shared issue templates. |

## Credits

PyKeko exists only because of the work it builds on:

- **[Moorhen](https://github.com/moorhen-coot/Moorhen)** — the Moorhen team's WebAssembly port of Coot and its React UI. PyKeko is a fork of their project, under their **BSD-3-Clause** license.
- **[Coot](https://www2.mrc-lmb.cam.ac.uk/personal/pemsley/coot/)** — Paul Emsley and the Coot developers (MRC-LMB), the original program this whole lineage descends from.
- **[CCP4](https://www.ccp4.ac.uk/)**, Clipper, MMDB, GEMMI, RDKit, and the other libraries Moorhen compiles to WebAssembly.

**Licenses:** PyKeko and PyKekoMCP are MIT; Moorhen-PyKeko follows upstream Moorhen's BSD-3-Clause license. See each repo's `LICENSE`.
