---
tags:
creation date: 26-08-19
assigned:
status: backlog
category:
  - before training
---
Found while running [[Full pipeline]] with a small smoke-test sweep over `scm/graph_type_edge_probs`.

- 6 of the 9 files in [scm/graph_type_edge_probs/](https://github.com/elidub/TFM/tree/18cd0cdd03a7680467869e8f86f92fb61775368d/gtfm/jobs/configs/scm/graph_type_edge_probs) — every `erdos_renyi_edgeprobbeta_*` and `gnr_edgeprobbeta_*` file — set `graph_edge_prob_alpha` / `graph_edge_prob_beta`, e.g. [erdos_renyi_edgeprobbeta_alpha6.0_beta2.0.yaml](https://github.com/elidub/TFM/blob/18cd0cdd03a7680467869e8f86f92fb61775368d/gtfm/jobs/configs/scm/graph_type_edge_probs/erdos_renyi_edgeprobbeta_alpha6.0_beta2.0.yaml)
- Those keys land in [generate.py's `get_tabicl_v2_prior_dataset`](https://github.com/elidub/TFM/blob/18cd0cdd03a7680467869e8f86f92fb61775368d/gtfm/gtfm/generate.py#L71), which builds a `tabicl` [`PriorConfig`](https://github.com/elidub/tabicl/blob/37bb1ab819002196ee8e4a1975e9d9a79b50211e/src/tabicl/prior/graph_lib/_config.py) from `scm_config`
- `PriorConfig` only has a single `graph_edge_prob` field (no alpha/beta split) — so any sweep that hits an `erdos_renyi`/`gnr` variant crashes:
  ```
  TypeError: PriorConfig.__init__() got an unexpected keyword argument 'graph_edge_prob_alpha'
  ```
- Only the 3 `cauchy_offset_*` files (which correctly use `graph_edge_prob`) work today
- Likely schema drift from the tabicl graphs-v1 → graphs-v2 rebuild — these files describe an older, Beta-distribution-parameterized `graph_edge_prob` that the current `PriorConfig` doesn't support, and nothing had exercised the `erdos_renyi`/`gnr` variants since

**To fix:** decide what `graph_edge_prob_alpha`/`beta` should map to under the current single-`graph_edge_prob` schema (a fixed value per file, like the `cauchy_offset_*` files? or does `PriorConfig`/`tabicl` need an alpha/beta option added back?), then update all 6 files accordingly.
