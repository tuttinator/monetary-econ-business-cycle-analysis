# LS3 business-cycle project

## Files

- `LS3_business_cycles.qmd` — Quarto/R notebook.
- `LS3_business_cycles.html` — self-contained rendered notebook.
- `LS3_business_cycles.pdf` — A4 PDF rendered with Quarto's bundled Typst engine.
- `business_cycles_d3.html` — interactive D3 visualisation.
- `d3_data/` — created by the notebook when it runs.
- `data/raw/` — dated GDP and expenditure-component snapshots created on the first render.
- `renv.lock` — exact R package versions used by the project.

## Run the notebook

Install Quarto and R. On a new machine, restore the project library from this
folder before rendering:

```bash
Rscript -e 'install.packages("renv", repos="https://cloud.r-project.org")'
Rscript -e 'renv::restore()'
quarto render LS3_business_cycles.qmd
```

The document is configured for both HTML and PDF, so the final command renders
both formats. To render just one format:

```bash
quarto render LS3_business_cycles.qmd --to html
quarto render LS3_business_cycles.qmd --to typst
```

The Typst command produces `LS3_business_cycles.pdf` without requiring a LaTeX
installation. The D3 page remains a separate interactive HTML document because
PDF cannot preserve browser interactivity.

The first render downloads quarterly seasonally adjusted real GDP from the
World Bank Global Economic Monitor and longer compatible IMF IFS series through
DBnomics.
It also downloads real private consumption and gross fixed capital formation
from IMF IFS through DBnomics. HTTP requests are handled inside R with `httr2`;
no command-line `curl` installation or API keys are required. The snapshots
are stored under `data/raw/`.

The unbalanced panel is requested from 1975 onward; each country and component
enters when its quarterly history becomes available. Normal renders reuse the
snapshots. To refresh the source data deliberately:

```bash
REFRESH_DATA=true quarto render LS3_business_cycles.qmd
```

After changing R dependencies, verify the render and update the lockfile:

```bash
Rscript -e 'renv::snapshot()'
```

The notebook computes log-HP cycles (lambda = 1600), volatility, persistence,
correlations with Indonesia, and rolling GDP correlations. It exports three
CSV files to `d3_data/`; the D3 page lets you switch among GDP, consumption,
and investment.

## View the D3 page

The interactive page is published on GitHub Pages at
<https://tuttinator.github.io/monetary-econ-business-cycle-analysis/>.
The workflow in `.github/workflows/pages.yml` runs on every push to `main`;
it publishes `business_cycles_d3.html` as the site's `index.html` alongside
`d3_data/`, the rendered notebook HTML and the PDF.

To view it locally after rendering the notebook, serve the project folder:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000/business_cycles_d3.html`.

If you open the HTML directly as a `file://` page, use its file pickers to load `d3_data/business_cycle_data.csv` and `d3_data/business_cycle_stats.csv`.
