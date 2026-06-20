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

## _What_ is PyKeko?

PyKeko is a macOS desktop application for the interactive macromolecular visualization and model building of structures determined by X-ray crystallography and cryo-EM.

Under the hood it is [**Moorhen**](https://github.com/moorhen-coot/Moorhen), Coot's C++ engine (libcootapi, CCP4, Clipper, MMDB, GEMMI, RDKit) compiled to WebAssembly and wrapped in a TypeScript/React interface. PyKeko packages that into a self-contained, one-click `.app`/`.dmg` and tunes it to feel like **Coot 0.9.x** - with similar keyboard shortcuts, defaults, and workflow primitives — while adding features of its own: a [PyMOL](https://www.pymol.org/) command-language scripting console and a PyMOL-`-R`-style RPC server, command-line integration, a residue torsion editor, Claude-MCP control, and more.

#### Named for the **[pūkeko](https://en.wikipedia.org/wiki/Australasian_swamphen)** bird — the Australasian swamphen — a New Zealand cousin of coots and moorhens.

> PyKeko is an independent project not affiliated with or endorsed by the Moorhen or Coot teams — but built on their extensive work (see [Credits](#credits)).

## _Why_ is PyKeko?

Coot 0.9.x renders through XQuartz/GLX, which does not work on recent macOS versions (**Tahoe** done broke it). PyKeko requires no XQuartz and no compiler — download the `.dmg` and go. **CCP4 is optional**: the core build / refine / save workflow is bundled, but if you do have CCP4 installed PyKeko transparently picks up its `acedrg`, `findligand`, and `refmac5` binaries for a handful of advanced features (SMILES → CIF dict fallback, ligand-site search, in-app REFMAC5 refinement of covalent links). A lot of crystallographers miss the the feel of pre-v1.0 Coot...

## Install (macOS, Apple Silicon)

**[⬇ Download the latest PyKeko.dmg](https://github.com/pykeko/Moorhen-PyKeko/releases/latest)** · **[Full install guide](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/install-mac.md)**

1. Open the DMG and drag **PyKeko** to your Applications folder.
2. First launch (the build is unsigned): right-click the app → **Open**, or run
   `xattr -dr com.apple.quarantine /Applications/PyKeko.app`.

Already on an earlier version? See **[upgrading](https://github.com/pykeko/Moorhen-PyKeko/blob/main/docs/install-mac.md#updating-to-a-newer-version)** — your settings and the `pykeko` command-line tool carry over.

Requires macOS 15.x (Tahoe) on Apple Silicon. The core app — Electron, the WebAssembly Coot engine, the monomer libraries — is fully bundled and works offline (except "Fetch from PDB").

**CCP4 is optional.** When CCP4 is installed (`/Applications/ccp4-9/` or on `PATH`) PyKeko transparently shells out to:

| CCP4 binary | What unlocks |
|---|---|
| `acedrg` | SMILES → CIF dictionary fallback when the in-app RDKit/WASM path can't handle a structure (`Ligand → New Ligand from SMILES…`) |
| `findligand` | `Ligand → Find ligand sites…` / `Find ligand here…` — Fo-Fc blob → ligand-fit |
| `refmac5` | The "Refine with REFMAC5…" button on the covalent-link panel (real-space refinement of declared covalent links against your MTZ) |
| `dimple` | `Ligand → Refine with DIMPLE…` — the auto-pipeline: rigid-body / MR + restrained refinement + optional ligand fitting, end-to-end. The "I have a co-crystal MTZ → refined complex" macro |

Without CCP4 those specific menu items become no-ops with a "CCP4 not installed" message; everything else in PyKeko works.

## Recent highlights

A rolling snapshot of the bigger additions across the 0.2.x line. **[Full per-release notes →](https://github.com/pykeko/PyKeko/releases)**

- **PyMOL-style trackball rotation** — Moorhen's Eulerian rotation (horizontal=yaw, vertical=pitch, no roll) replaced as default with a line-for-line port of PyMOL's Shoemake virtual trackball using the same constants (`mouse_scale=1.3`, `mouse_limit=100`, radius `0.45×min(W,H)`). Feels like spinning a ball with your fingers, with natural edge-roll. Preferences → Mouse sensitivity… toggle to switch back to the Coot/Moorhen gimbal feel if you want it.
- **DIMPLE auto-pipeline** — new `Ligand → Refine with DIMPLE…` wraps CCP4's `dimple` (rigid-body / MR + restrained refinement + optional ligand fitting end-to-end). Load apo model, optionally a ligand from SMILES, click → pick MTZ → dimple runs in the background (typical 2–10 min, progress in the in-app log console) → refined model loads back automatically.
- **In-app log console + scripting REPL** — a thin always-on strip at the bottom of the viewport tails PyKeko's log so you can see what the app is doing without dropping to Terminal. **⌘\`** expands it into a scrollable panel with filter + colour-coded levels, plus a REPL with a **JS / PyMOL** mode dropdown. The `!` prefix runs the input through your login shell with `~/.zshrc` sourced (so conda init, CCP4 setup, custom PATH all work); `!cd`, `!pushd`/`!popd`, `!export`, and `!clear` mutate process-wide state so subsequent saves and spawn helpers (refmac5 / findligand / acedrg) honour them.
- **Covalent-ligand workflow + in-app REFMAC5** — `Ligand → Make covalent…`: pick a Cys SG and a ligand Cβ; PyKeko auto-detects the warhead family (acrylamide / α,β-ynamide / chloroacetamide / epoxide / maleimide / Mike-acceptor-vinyl), declares the link, and updates the displayed bond orders to match the post-reaction state. With CCP4 installed, one more click refines the model against your MTZ via the system's `refmac5`. `Ligand → Find ligand sites…` likewise drives CCP4's `findligand` to search Fo-Fc for ligand-shaped blobs.
- **Real desktop session save/restore** — `File → Save session…` writes a single `.pykeko` file (full scene: molecules, maps, per-rep colour rules, camera, vectors, 2D overlays, view settings); `Open session…` rehydrates it. Drag-drop onto the window also works.
- **Portable Mol\* viewer export** — `File → Export portable viewer (.html)` produces a single self-contained HTML file (Mol\* under the hood) that reproduces your visible scene (representations, colour rules, ligand spheres, density …) for sharing or embedding. Optional density map embedding gives a "rolling cube" that tracks the recipient's camera, à la Coot.
- **Shell-style scripting history** — `Calculate → Interactive scripting`'s modal now has per-mode `↑/↓` history, `Cmd/Ctrl+Enter` to submit, and persistence across reloads. Works for both PyMOL and JavaScript modes.
- **Command-line integration** — `pykeko model.pdb data.mtz ligand.cif` opens files (the `.cif` attaching as a *dictionary*, not spawning a new molecule); `pykeko 7sj3` fetches by PDB id; `pykeko script.pml` runs a PyMOL-style script. A second launch of `pykeko` hands its files to the running window (PyMOL `-R` style); `--new` opens a fresh one. Install the command from **Preferences → Install command-line launcher**.
- **Residue torsion editor** — right-click a residue → **Edit torsions**: rotate φ/ψ/χ angles with a **live Ramachandran plot** tracking the sliders.
- **`pykeko_remote.py`** — a PyMOL-`-R`-style Python client that drives a running PyKeko from your own scripts (load / refine / screenshot / run PyMOL commands / …).
- **Coot-style defaults** — black background, hydrogens shown when present, PyMOL as the default scripting language. Updated keyboard shortcuts shown with `'m'`.

## Capabilities

**Model building (inherited from Coot, via Moorhen).** Real-space refinement (single → triple → sphere → chain → all), rotamer fitting, peptide flips, mutations, add/delete of atoms and residues, terminal-residue building, jiggle fit, ligand fitting, map manipulation and contouring, geometry/validation analysis, and reading/writing PDB, mmCIF, MTZ, and CCP4/MRC maps — all locally, no server.

**Added by PyKeko on top of Moorhen:**

| Area | What you get |
|---|---|
| **Command line** | open files, fetch by PDB id, run `.pml` scripts, ligand-dictionary auto-attach, single-instance file handoff |
| **PyMOL scripting** | run PyMOL commands *inside* the app — a command-language translator in *Interactive Scripting*: full selection algebra, representations, colours, measurements, and settings; `.pml` files run directly |
| **PyMOL RPC server** | drive a running PyKeko *from outside* — a built-in localhost RPC control server (PyKeko's take on PyMOL's `-R`), scripted from Python via the bundled [`pykeko_remote.py`](https://github.com/pykeko/PyKeko/tree/main/remote) client, or from Claude via [PyKekoMCP](https://github.com/pykeko/PyKekoMCP) |
| **Torsion editor** | backbone φ/ψ + sidechain χ sliders with a live Ramachandran plot |
| **NCS ghosts** | overlay every NCS-related chain transformed onto a chosen master chain (`g`) |
| **Cyclers** | step through validation outliers (`n`), difference-map peaks (`p`), ligands (`l`), and NCS mates (`o`) straight from the keyboard |
| **Coot ergonomics** | Coot 0.9-style shortcuts and defaults, single-water-at-crosshairs (`w`), drag-atoms-with-refinement (`d`) |

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
