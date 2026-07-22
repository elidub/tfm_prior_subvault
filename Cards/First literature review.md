Useful papers about synthetic data generation:

- [[@turkmenEvaluatingDataPriors2026]]
- [[@leeuwenWhenDataScarce2026]]
- [[@bouadiWhatYouPretrain2026]]
- [[@bouadiShapingPriorHow2026]]




| Model →                          | GCFM                                         | TabPFNv2                                  | TabICLv2                                                           |
| -------------------------------- | -------------------------------------------- | ----------------------------------------- | ------------------------------------------------------------------ |
| **DAG sampling**                 | Erdos–Rényi($p_\text{edge}, n_\text{nodes}$) | GNR($p_\text{edge}, n_\text{nodes}$)      | $p_{ij} = \operatorname{sigmoid}(A + B_i + C_j)$,                  |
| **Edge prob** ($p_\text{edge}$)  | $\text{Beta}(2, 3)$                          | $\text{Gamma}(\alpha, \beta)$             | $A$, $B_i$, $C_j$ are independent standard Cauchy random variables |
| **Node dist** ($n_\text{nodes}$) | $\mathcal{U}(2, 52)$                         | $\text{log-}\mathcal{U}(a, b)$<br>        | $\text{log-}\mathcal{U}(2, 32)$                                    |
| **Feature dist**                 | equal to node (i.e.  no missing nodes)       | $\text{Beta}(0.95, 8)$, scaled to $1-160$ | $\mathcal{U}(2, 100)$<br>                                          |
