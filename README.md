# AdvBridge
AdvBridge is a targeted data poisoning method for social recommendation. It constructs budget-constrained interaction and social profiles for controlled users to promote a target item to a specific target user.

## Overview

AdvBridge combines a diffusion-enhanced surrogate with dual-anchor profile optimization. The surrogate learns user and item representations from a clean graph using MHCN and cross-guided dual diffusion. These representations guide the construction of interaction and social connections around the target user and target item.

The method first builds candidate pools using embedding similarity, PageRank, local interactions, and item popularity. It then alternates between bridge/fidelity optimization and ranking/concentration optimization under degree budgets. Continuous profile weights are projected onto capped-simplex constraints and converted into discrete connections through Top-K selection. A fresh victim without diffusion is trained on the modified graph to evaluate the attack.

## Data Format

Place prepared files in the following directories:

```text
datasets/
├── lastfm/
│   ├── ratings.txt
│   └── trusts.txt
├── yelp/
│   ├── ratings.txt
│   └── trusts.txt
└── douban/
    ├── ratings.txt
    └── trusts.txt
```

Each interaction row contains:

```text
user_id item_id rating
```

Each directed social edge contains:

```text
source_user destination_user
```

Whitespace, tabs, and commas are accepted as separators. An optional third social column is ignored; social edges have weight 1. User IDs must be nonnegative integer strings with an unambiguous numeric spelling. Item IDs may be nonempty strings. Retained user-item interaction pairs must be unique. No header is expected; blank lines and full-line `#` comments are ignored.

| Configuration | Retained rating threshold | Training epochs | Example target user / item |
| --- | ---: | ---: | --- |
| [Lastfm](configs/lastfm.json) | 1 | 120 | 46 / 54 |
| [Yelp](configs/yelp.json) | 1 | 30 | 176 / 223 |
| [Douban](configs/douban.json) | 4 | 30 | 117 / 261 |

Retained ratings are converted to 1. Douban additionally enables normalized self-supervised representations. Benchmark datasets are not bundled; obtain the data from their providers and retain your preprocessing record. Further input conventions are documented in [datasets/README.md](datasets/README.md).

Keep the prepared interaction order fixed. Retained row `i`, indexed from zero, belongs to held-out fold `i % folds + 1`. The target user and item must exist in every selected clean training fold.

## Evaluation and Outputs

Recommendation quality is measured by precision, recall, F1, and NDCG at `training.top_n`. Attack metrics include:

| Metric | Definition |
| --- | --- |
| `target_hit` | Whether the target item appears in the target user's Top-N |
| `target_rank` | Target-item rank among eligible, unconsumed items |
| `target_score` | Raw target user-item dot product |
| `non_target_exposure_count` | Target-item appearances among evaluated non-target users |
| `non_target_exposure_rate` | Exposure count divided by the number of evaluated non-target users |
| `sr_folds`, `sr_rate` | Target hits and mean target hit rate over the selected folds |

Consumed items are excluded from recommendation. A consumed target item has no eligible rank (`null`) and cannot be counted as a hit. Recommendation metrics use held-out users; the target user's list is also computed if that user has no held-out row. `legacy_exposure_probability` separately retains the source normalization using all held-out evaluation users as its denominator.

Epoch selection follows the source protocol: compare precision, recall, F1, and NDCG on the held-out fold using a majority comparison, with ties following the source rule. There is no separate validation split.

A full run saves:

```text
runs/example/
├── manifest.json
├── fold_metrics.csv
├── summary.json
└── fold_1/
    ├── surrogate_training.json   # Present when trained in this run
    ├── injected_ratings.tsv
    ├── injected_social.tsv
    ├── attack.json
    ├── victim_training.json
    ├── recommendations.json
    └── metrics.json
```

`manifest.json` records the effective configuration, source/data hashes, dependency versions, selected folds, cache locations, and run status. Per-fold files record actual injected edges, optimization losses, epoch histories, and final metrics. `summary.json` aggregates only the selected folds. Shell redirection can be used to retain the full console log.

Surrogates are cached under `.cache/surrogates` by default. Cache identity includes the data, fold, training settings, relevant source files, and runtime versions. ID ordering, dimensions, and finite values are checked before reuse. Targets and attack-only settings do not change clean surrogate training.

## Acknowledgments

The MHCN backbone and recommendation training protocol build on [QRec](https://github.com/Coder-Yu/QRec).
