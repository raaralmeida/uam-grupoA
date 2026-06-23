# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static multi-page website (no build step, no package manager) for an academic project on **Urban Air Mobility (UAM)** in Brazil, developed for course IT-214 by Grupo A. The site analyzes UAM demand, selects Brasília as the target city, and proposes vertiport locations.

To view the site locally, open any `.html` file directly in a browser or serve with any static file server:

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Architecture

**Every page is a self-contained HTML file** — no templating engine, no framework, no JS module bundler. The shared structure follows a copy-paste pattern:

- **Navbar** — duplicated verbatim in every active page; must be kept in sync manually across all files when links change.
- **`css/styles.css`** — global stylesheet loaded by all pages (dark theme using CSS custom properties, Bootstrap 5 grid, Inter font).
- **Page-specific CSS** — most pages load an additional stylesheet from `css/<page-name>.css`.
- **`js/scripts.js`** — intentionally blank placeholder; page-specific JS is written inline at the bottom of each HTML file.
- **Assets** — `img/` holds page content images; `assets/img/` holds layout/showcase images used in `index.html`.

## CDN Dependencies (all loaded remotely)

| Library | Version | Usage |
|---|---|---|
| Bootstrap | 5.2.3 | Grid, utilities |
| AOS (Animate On Scroll) | 2.3.4 | Scroll animations |
| Chart.js | latest | Charts in demand/selection pages |
| Inter (Google Fonts) | — | Typography |

## Page Map

| File | Section in navbar | Notes |
|---|---|---|
| `index.html` | Home | Hero with YouTube embed + image fallback |
| `fundamentacao_teorica.html` | Revisão | Uses Chart.js |
| `exercicio.html` | Revisão | Uses Chart.js |
| `selecao_cidade.html` | Análises | Uses Chart.js |
| `caracterizacao_brasilia.html` | Análises | Uses Chart.js |
| `selecao_vertiporto.html` | Análises | Uses Chart.js; loads `css/selecao_vertiporto.css` |
| `mapas_qgis.html` | Análises | Loads `css/mapas_qgis.css` |
| `demanda_UAM.html` | Demanda de UAM | Uses Chart.js; loads `css/demanda_UAM.css` |
| `memorial_vertiporto.html` | Demanda de UAM | Loads `css/memorial_vertiporto.css` |
| `analise_meteorologica.html` | Demanda de UAM | Inline styles only |

**`html sem função/`** — archived/unused HTML files; not linked from the navbar.

## Key Conventions

- Language: Portuguese (pt-BR) throughout HTML content and comments.
- Dark theme: background `#0a0a0a`, white text, defined via CSS variables in `styles.css:10-20`.
- Navbar scroll effect: JS at the bottom of each page adds `.scrolled` class after 60px scroll; the navbar background becomes `rgba(10,10,10,0.97)`.
- AOS animations: initialized with `{ duration: 900, once: true, offset: 60 }` inline in each page.
- `demanda_uam.html` vs `demanda_UAM.html`: the navbar links to `demanda_uam.html` (lowercase) but the file is `demanda_UAM.html` — watch for case sensitivity on Linux.
