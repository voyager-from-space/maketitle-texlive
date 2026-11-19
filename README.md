# maketitle-texlive

The TeX Live tree that [maketitle](https://github.com/voyager-from-space/maketitle) compiles with, cut up so a browser only downloads the files a paper actually opens.

- `index-*.json.gz` lists all 23,796 files: directory, name, size, and which pack holds them. It also lists a small "base" set of packs that most papers need, and the `\RequirePackage` links between `.sty`/`.cls` files.
- `core-*.bin` (gzip) holds the few config files that are mounted at start (`texmf.cnf`, `ls-R`, fontconfig files).
- `p/xx/<hash>.bin` are gzip packs of at most 512 KB of files from one directory (128 KB for font directories). Names are content hashes, so they never change.
- `e/` holds the BusyTeX engine (`busytex.wasm`) and biber, gzipped.

In the page's worker every TeX Live file is created as a lazy node: `stat` answers from the index, and the first read of a file fetches its pack. Before a compile the packs the source clearly needs are fetched in parallel. A plain article needs about 19 MB in total, engine included, instead of the whole 520 MB tree.

Files are served by jsDelivr's GitHub CDN (pinned to a commit) with this repository's GitHub Pages site as the fallback. Both send `access-control-allow-origin: *`.

Rebuild: `scripts/texlive/` in the maketitle repo (`extract.cjs` dumps the BusyTeX data packages, `build-tree.mjs` writes this tree, `mkconfig.mjs` prints the app config).

## Licences

The engine is [BusyTeX](https://github.com/busytex/busytex) / [texlyre-busytex](https://github.com/TeXlyre/texlyre-busytex) (AGPL-3.0). The TeX Live files keep their own licences (LPPL, GPL, OFL, public domain and others); see `LICENSE.TL` and `LICENSE.CTAN` inside the tree and each package's headers. Nothing here was changed except `fonts.conf`, which no longer lists the Type 1 font directory for fontconfig.
