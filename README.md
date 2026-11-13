# Company address concentrations in the Antwerp region

An open-source analysis of where companies cluster at the same address, and how many of those clusters survive a check against other open sources.

**→ The findings are in the Dutch report: [RAPPORT.md](RAPPORT.md).**

Headline results (KBO extract of 5 November 2026, province of Antwerp plus the left-bank port area):

- 96.7% of company addresses linked to the official address register (BeST); the KBO's habit of appending sub-municipality abbreviations to street names explains most corrections.
- Addresses with many companies are dominated by established intermediaries (accountants, business centres), not by clusters of new firms: the share of sole proprietorships falls from 35% to 8% as concentration rises, while the share of recent starts does not rise.
- One explicit selection rule picked 54 addresses; a first pass over the top 15 explained most of them as fund, project or residential structures. The one address left standing was a probable private residence, where the analysis deliberately stops.

The project is a methods exercise in open-source analysis: source criticism, record linkage against an official register, transparent indicators, explicit treatment of false positives, and knowing where to stop. It does not identify or accuse any business or person.

## Notebooks

| # | Notebook | What it does |
|---|----------|--------------|
| 01 | `01_load_and_prepare.ipynb` | Loads the KBO extract into DuckDB, checks the schema, normalises addresses and contact details, builds the link table and a first network |
| 02 | `02_address_quality.ipynb` | Links KBO addresses to BeST in four stages, reports the match rate and why the rest fails, adds coordinates and a building-size proxy |
| 03 | `03_concentrations_and_review.ipynb` | Profiles addresses by concentration level, applies one selection rule, produces a review sheet and summarises the manual check |

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
nbstripout --install          # strips notebook outputs on commit
```

1. Register on the [KBO Open Data portal](https://kbopub.economie.fgov.be/kbo-open-data/), accept the licence, and download a full extract (`KboOpenData_XXXX_YYYY_MM_DD_Full.zip`) into `data/raw/`.
2. Download the BeST Address CSV (NGI/geo.be, Lambert 2008), unzip it and put the CSV in `data/raw/best/`. Derived BeST CSVs with WGS84 columns also work.
3. Run the notebooks in order.

```
kbo-osint/
├── README.md
├── RAPPORT.md                 report (Dutch)
├── requirements.txt
├── figures/                   aggregated figures only
├── notebooks/
│   ├── 01_load_and_prepare.ipynb
│   ├── 02_address_quality.ipynb
│   └── 03_concentrations_and_review.ipynb
└── data/                      git-ignored, created by the notebooks
    ├── raw/                   KBO zip + best/*.csv
    ├── interim/               unzipped CSVs + kbo.duckdb
    ├── processed/             parquet outputs
    └── review/                manual review sheet (real addresses: never commit)
```

## Data, licence and privacy

**Source.** KBO Open Data, FOD Economie, K.M.O., Middenstand en Energie (Kruispuntbank van Ondernemingen), extract 0535, snapshot 05-11-2026. Addresses: BeST Address, BOSA. All derived tables, keys and indicators are the author's own processing; the source data itself is used unchanged.

Use follows the *Licentie Open Data: gebruiksvoorwaarden*:

- The licence is personal and non-transferable (art. 2.6, 11), so KBO data is not redistributed here. `data/` is git-ignored.
- Reuse is limited to the purpose stated at registration (art. 2.3): non-commercial research into company registration patterns.
- Raw files are kept unaltered; normalised addresses and indicators are derived fields, never presented as KBO data (art. 2.7).
- Source and update date are stated with every reuse, including figures (art. 2.8).
- No use for direct marketing (art. 2.2).
- Sole proprietorships are registered under a natural person's name, so the data contains personal data (GDPR, art. 2.1). With `PUBLIC_MODE = True` the notebooks only display pseudonymised keys, and outputs are stripped before commit.
- Results are published only in aggregate. Addresses where the analysis reaches private individuals are not described individually and not investigated further.

## Known limitations

- One snapshot of active entities: no history, no stopped companies. Churn cannot be measured.
- Directors and mandates are not in the open data, so group structures are only visible through names.
- Address normalisation is rule-based; notebook 02 measures how often it fails against BeST.
- BeST address units are registry units, not floor space: a single-unit building can be a large office.
- The selection rule was checked on 15 addresses: a first impression, not a validation.
