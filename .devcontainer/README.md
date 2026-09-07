# Devcontainer for `hpc_talk`

Builds the deck inside the official-ish `texlive/texlive:latest` image (see
the repo README for what that is). The theme's system fonts and the Catppuccin
Pygments styles, which the README installs by hand, come from
`.devcontainer/setup-hpc-talk.sh` (also used by the `postCreateCommand` in
`devcontainer.json`).

## Usage

**VS Code**: "Reopen in Container" (⌘⇧P → *Dev Containers: Reopen in Container*).

**`devcontainer` CLI** (or any Dev Containers CLI):

```sh
devcontainer build --workspace-folder . \
    --image-name hpc-talk:dev
devcontainer up --workspace-folder . \
    --remove-existing-container \
    --mount-workspace-git-root false
```

`devcontainer.json` picks the default user-scope mount, so the container
works on the repo itself.

**First build** clones `slidemint`, `macromint`, and `figmint` from GitHub and
runs `l3build install` on each — the only slow step (~1–2 min).

## Build the deck

```sh
rebuild        # = latexmk slides.tex, from the repo root
```

The `latexmkrc` handles LuaLaTeX mode and puts the venv-style tools on `PATH`.

## What's inside

- TeX Live 2026 `full` scheme: `latexmk`, `lua(la)tex`, `beamer`, `minted`,
  `tlmgr`, `l3build`
- `python3` with `pip`, `Pygments`, and `catppuccin[pygments]`
- System fonts used by `slidemint`: Plus Jakarta Sans, Roboto Condensed,
  MesloLGS NF, Noto Sans Math
- The author's `slidemint`, `macromint`, `figmint` packages (installed to the
  TeX user tree via `l3build install`)

## Notes / caveats

- The image is Debian *sid*, so `apt` pulls unstable packages. Setup overlays
  `sid` into `/etc/apt/sources.list.d/` because the shipped image already
  needs `sid non-free` apt lines and the base image's sources didn't have them.
- TeX Live fonts live in a *per-release* tree: `tlmgr install` writes to the
  TeX Live 2026 tree, which means **every** TeX Live *major* release requires
  re-copying the three `l3build install`s and any `tlmgr`-installed fonts into
  the new tree. Keeping setup as a `postCreateCommand` makes that one-line to
  rerun ("Rebuild Container").
- `latest` is rebuilt weekly; pin a `TL20XX-…` tag or image digest in
  `devcontainer.json` for reproducible builds.

## License

Apache 2.0 (see repo root).