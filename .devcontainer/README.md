# Devcontainer for `hpc_talk`

Builds the deck inside a container based on `texlive/texlive:latest`, with the
tooling, fonts, and LaTeX packages the deck needs baked into the image at build
time. Edits and build outputs (`slides.pdf`) live on the host via the standard
devcontainer workspace bind mount.

## Usage

**VS Code**: open the repo, then Command Palette →
*Dev Containers: Reopen in Container* (or *Rebuild Container*).

**`devcontainer` CLI** (or any Dev Containers CLI):

```sh
devcontainer build --workspace-folder .
devcontainer up --workspace-folder .
```

The container runs as `root` (`remoteUser`), and the workspace is bind-mounted
at `/workspaces/hpc_talk`. Extensions are installed into the container on first
open, per `customizations.vscode.extensions` in `devcontainer.json`.

## Build the deck

```sh
latexmk slides.tex      # or Rebuild from the LaTeX Workshop UI
```

`latexmkrc` sets LuaLaTeX mode. Output lands at `slides.pdf` in the repo root
(and is written back to the host through the bind mount).

## What's inside

- TeX Live `full` scheme: `latexmk`, `lua(la)tex`, `beamer`, `minted`, `tlmgr`,
  `l3build`
- `python3` with `Pygments` and `catppuccin[pygments]` (global, for `minted`)
- System fonts used by `slidemint` (fetched from Google Fonts / GitHub at build
  time, hosted in `/usr/local/share/fonts/hpc-talk`):
  - Plus Jakarta Sans (+ Italic)
  - Roboto Condensed
  - MesloLGS NF (Regular, Bold, Bold Italic, Italic)
  - Noto Sans Math (Regular)
- The author's `slidemint`, `macromint`, and `figmint` packages, installed to
  the TeX user tree (`/root/texmf`) via `l3build install`
- Small Debian tools: `git`, `wget`, `unzip`, `coreutils`

## Notes / caveats

- **The base image is Debian `testing`** (currently `forky`). The `apt` setup
  uses the image's stock sources; nothing is overlaid.
- **Fonts that are not Debian packages** (Plus Jakarta Sans, Roboto Condensed,
  MesloLGS NF, Noto Sans Math) are downloaded from their upstream Google
  Fonts / GitHub locations at build time. The URLs are pinned to
  `google/fonts` `main` and `romkatv/powerlevel10k-media` `master`; pin a
  specific commit for fully reproducible builds.
- **SELinux (Fedora):** the devcontainer runs with
  `--security-opt label=disable` (in `devcontainer.json`) so the bind-mounted
  workspace is writable inside the container despite the host's SELinux labels.
  This is a deliberate, container-scoped relaxation; the container itself has
  no other escalated privileges.
- **`containerEnv.PATH`** is set explicitly (standard bin dirs +
  `/usr/local/work/bin`) rather than using `${containerEnv:PATH}`, which the
  Dev Containers CLI can resolve to the *host* PATH and break the container's
  shell (e.g. `sleep: not found`).
- **TeX Live is per-release:** `tl` majors move font/package trees; rebuilding
  the image picks up new TeX Live releases. `latest` is rebuilt upstream
  weekly — pin a `TL20XX-…` tag or image digest for reproducible builds.
- **Extensions** install into the container on first open; adding one to
  `customizations.vscode.extensions` takes effect on the next open/rebuild
  without rebuilding the image.
- The upstream `texlive` image is Debian `sid`/`testing`, so `apt` may pull
  unstable packages — expected for a bleeding-edge TeX environment.

## License

Apache 2.0 (see repo root).