# ab-test-data-audit

**All data in this project is synthetic.** The base tables come from a Kaggle dataset that is itself synthetic. On top of that, a seeded generator injects data-quality problems (timing errors, group-assignment issues, missing codes, messy values) that are recorded in a ground-truth manifest. Nothing here is evidence about a real marketing campaign. The project shows how to audit experiment data and how to analyze it, and whether the analysis recovers effects that were planted on purpose.

## What this project is for

To show, with SQL and Python, that I can:

1. Check whether an experiment's data can support a causal claim before analyzing it (timing, assignment, covariate balance, codes).
2. Document what each check found and what it could not check.
3. Estimate the treatment effect on the cleaned data and compare it with the planted `expected_uplift` values.

## Planned steps

1. **Generator** (`src/generate.py`): reads the base tables, injects known problems at set rates with a fixed seed, and writes the modified tables plus `data/ground_truth.json` listing every injected issue.
2. **Audit in SQL** (`sql/`): one query file per check, written to be read in order:
   - timing: events dated before their campaign's start date
   - assignment: customers in more than one group, group split, covariate balance
   - codes: meaning and count of `campaign_id = 0`
   - messiness: missing device types, inconsistent casing in traffic source
3. **Recovery check**: compare audit findings with `ground_truth.json`. Report what was caught, what was missed, and any false positives.
4. **Analysis** (`notebooks/` or `src/`): intent-to-treat conversion and revenue by group, covariate-adjusted, on the cleaned data. Compare the estimate with `expected_uplift`.
5. **Write-up**: what the data supports, what it does not, and which problems were planted versus present in the source.

## Status

Planning stage. No generator or queries exist yet.

## Data

- `data/raw/`: base tables (gitignored; source is a Kaggle dataset, which should be cited with its link once confirmed)
- `data/` generated tables and `ground_truth.json` (gitignored; reproduced by running the generator with the seed in the config)

## Reproducing

Planned: `python src/generate.py --seed 20261116` writes the synthetic tables and manifest. The seed is fixed so anyone rerunning gets the same data.
