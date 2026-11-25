# maketitle-texlive

The TeX Live tree that [maketitle](https://github.com/voyager-from-space/maketitle) compiles with, cut up so a browser only downloads the files a paper actually opens.

- `index-*.json.gz` lists all 23,796 files: directory, name, size, and which pack holds them. It also lists a small "base" set of packs that most papers need, and the `\RequirePackage` links between `.sty`/`.cls` files.
- `core-*.bin` (gzip) holds the few config files that are mounted at start (`texmf.cnf`, `ls-R`, fontconfig files).
- `p/xx/<hash>.bin` are gzip packs of at most 512 KB of files from one directory (128 KB for font directories). Names are content hashes, so they never change.
- `overlay-*.json.gz` and `x/<hash>.bin` add CTAN packages the BusyTeX set lacks (inconsolata, newtx, algorithms, algorithmicx, kastrup, llncs, doublestroke, bbm, bbm-macros, cyrillic, elsarticle, mhchem, chemgreek, velthuis) without changing any main pack, plus the lines they need in `ls-R` and `pdftex.map`. `cmttb10` (bold typewriter) and the `bbm` fonts exist only as METAFONT sources, which cannot run in a browser: they are drawn with `cmtt10` and the doublestroke outlines, and the page says so.
- `e/` holds the BusyTeX engine (`busytex.wasm`) and biber, gzipped.

In the page's worker every TeX Live file is created as a lazy node: `stat` answers from the index, and the first read of a file fetches its pack. Before a compile the packs the source clearly needs are fetched in parallel. A plain article needs about 19 MB in total, engine included, instead of the whole 520 MB tree.

Files are served by jsDelivr's GitHub CDN from two half-trees, [maketitle-tl-a](https://github.com/voyager-from-space/maketitle-tl-a) and [maketitle-tl-b](https://github.com/voyager-from-space/maketitle-tl-b) (pinned to the tag `tl1`; each stays under jsDelivr's 150 MB limit for a GitHub repository), with this repository's GitHub Pages site, which holds the whole tree, as the fallback. Both send `access-control-allow-origin: *`.

Rebuild: `scripts/texlive/` in the maketitle repo (`extract.cjs` dumps the BusyTeX data packages, `build-tree.mjs` writes this tree, `fetch-tl.sh` and `build-overlay.mjs` add CTAN packages, `mkconfig.mjs` prints the app config).

## Licences

The engine is [BusyTeX](https://github.com/busytex/busytex) / [texlyre-busytex](https://github.com/TeXlyre/texlyre-busytex) (AGPL-3.0). The TeX Live files keep their own licences (LPPL, GPL, OFL, public domain and others); see `LICENSE.TL` and `LICENSE.CTAN` inside the tree and each package's headers. Nothing here was changed except `fonts.conf`, which no longer lists the Type 1 font directory for fontconfig.
