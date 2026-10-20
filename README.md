# AquaRev Specifications pages — Deployment

Two pages on **aquarevwater.us** (Webflow), same split-deploy pattern as the Data,
Water Hardness and Certification pages: CSS + JS hosted on GitHub Pages, a short
embed pasted into each Webflow page. Every style is scoped to its container, so
Webflow's global styles do not reach the page.

| Page | Slug | Container | Files |
|---|---|---|---|
| Specifications & Installation Instructions (document) | `/specifications` | `#arsp` | `specifications.css`, `specifications.js`, `webflow-embed-specifications.html` |
| Installation Drawing Set 100 (eight sheets) | `/drawings` | `#ardw` | `drawings.css`, `drawings.js`, `webflow-embed-drawings.html` |

The specifications page carries a card that links to the drawing set page and to
the drawing set PDF; the drawing set page links back to the specifications page
and its PDF. Both PDFs are served from this repo on GitHub Pages. If either slug
changes, edit `SITE_SPEC` / `SITE_DRAW` at the top of `build.sh`, rebuild and push.

## Masters (edit these, never the generated files)

| Master | Location |
|---|---|
| Specifications & Installation Instructions | `Marketing/Collateral/Installation Drawings/AquaRev_Installation_Specifications_V1.html` |
| Drawing Set 100 | `Marketing/Collateral/Installation Drawings/AquaRev_Installation_Drawing_Set_V1.html` |
| PDFs | Same folder; re-render with the Chrome command in that folder's README before rebuilding here |

## Generated files (this folder)

`build.sh` regenerates everything from the masters. Each JS file injects its page
markup into the empty container. Artifact links in the masters become site links;
local PDF links become GitHub Pages links; the "local web copy" button is dropped.

`preview.html` and `preview-drawings.html` mirror the two embeds with local files.

## PDFs are Webflow assets

The PDFs the site pages link to live in the Webflow asset library (folder
**Engineering**), not on GitHub Pages. `webflow-assets.json` records each asset's
ID, hosted URL and the MD5 of the uploaded file; `build.sh` rewrites the PDF links
in the generated JS to those URLs and prints a WARNING when a local PDF no longer
matches its recorded MD5.

| PDF | Webflow asset ID |
|---|---|
| AquaRev_Installation_Specifications_V1.pdf | 6ad6c947c794481b4863475f |
| AquaRev_Installation_Drawing_Set_V1.pdf | 6ad6c947ae34160edcb559af |
| AquaRev_Implementation_Guide_V1.pdf | 6ad6c9481bfbe093bbb37f8b |

**When a PDF changes.** Webflow has no replace-file API, so the new file is uploaded
as a new asset (new ID and URL). Steps: re-render the PDF from its master; upload it to
the Engineering folder (Claude does this through the Webflow connector: `create_asset`
with the file's MD5, then POST the bytes to the returned S3 URL); paste the new
`hostedUrl` and `md5` into `webflow-assets.json`; run `./build.sh`; commit and push.
The old asset can then be deleted from the Webflow asset panel. The Webflow embeds do
not change, because the URLs live in the JS on GitHub Pages.

The GitHub Pages copies of the two PDFs are still published for the private
claude.ai review pages, which link to them.

## GitHub Pages

- Repo: `jeffatley-web/aquarev_specifications` (public), Pages source `main` branch, root.
- https://jeffatley-web.github.io/aquarev_specifications/specifications.css
- https://jeffatley-web.github.io/aquarev_specifications/specifications.js
- https://jeffatley-web.github.io/aquarev_specifications/drawings.css
- https://jeffatley-web.github.io/aquarev_specifications/drawings.js
- https://jeffatley-web.github.io/aquarev_specifications/AquaRev_Installation_Specifications_V1.pdf
- https://jeffatley-web.github.io/aquarev_specifications/AquaRev_Installation_Drawing_Set_V1.pdf

## Webflow page notes

- Create the two pages (`specifications`, `drawings`). On each, drop an Embed
  element into a full-width section and paste the matching `webflow-embed-*.html`.
  Publish.
- The embeds set `data-theme="dark"` on the container to match the other document
  pages. Change it to `data-theme="light"` for a white page; the CSS supports both.
- The drawing sheets themselves are always white paper, whatever the theme.
- Fonts (Archivo, Source Sans 3, IBM Plex Mono) load from Google Fonts via the
  first `<link>` in each embed.

## Update workflow

1. Edit the master(s) in `Marketing/Collateral/Installation Drawings/` and
   re-render the PDFs there.
2. Regenerate:

   ```bash
   cd "/Users/jatley/Documents/Jeff Atley/Aquarev Water /Marketing/Website/Specifications"
   ./build.sh
   ```

3. Commit and push:

   ```bash
   cd "/Users/jatley/Documents/Jeff Atley/Aquarev Water /Marketing/Website/Specifications"
   git add -A
   git commit -m "Update specifications pages"
   git push
   ```

4. `build.sh` stamps a fresh `?v=` cache buster into both embeds each run.
   Re-paste an embed into Webflow when you want browsers to pick up a change
   immediately; otherwise the GitHub Pages cache refreshes within about ten
   minutes on its own.

The published Claude artifact versions are the visual reference:
specifications https://claude.ai/artifact/GAtq5SW7eoTDZ63EZjBkMD and
drawings https://claude.ai/artifact/MXuMZFfC1exQmvcYonPKDo.

## Before publishing live

The drawings are a reference set drawn around a sample property; every title block
says "reference set, not a site-specific drawing". That is intended for a public
reference page. The IAPMO listing name and model-ID inconsistencies noted in the
Implementation Guide memory still apply to any submittal built from these pages.
