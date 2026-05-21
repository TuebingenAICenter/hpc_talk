# HPC Talk

Slides for a talk I gave on HPC in Europe through an academic's lens.

## Setup

Install the TeX packages once:

```sh
git clone https://github.com/bmucsanyi/slidemint.git
git clone https://github.com/bmucsanyi/macromint.git
git clone https://github.com/bmucsanyi/figmint.git

cd slidemint && l3build install && cd ..
cd macromint && l3build install && cd ..
cd figmint && l3build install && cd ..
```

Create the deck-local Python environment used by `minted`:

```sh
python3 -m venv .venv
.venv/bin/python -m pip install "catppuccin[pygments]"
```

The deck expects a current TeX Live with `latexmk`, LuaLaTeX, Beamer, BibTeX,
`minted`, and the fonts used by `slidemint`.

## Build

```sh
latexmk slides.tex
```

The project `latexmkrc` sets LuaLaTeX mode and puts `.venv/bin` on `PATH` so
`minted` can find the Catppuccin Pygments styles.

## License

Apache 2.0. Graphics are all rights reserved.
