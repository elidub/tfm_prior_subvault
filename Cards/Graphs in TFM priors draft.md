
## Literature
## Definitions


| Term                   | Explanation                                                            |
| ---------------------- | ---------------------------------------------------------------------- |
| Downstream performance | Performance of pre-trained TFMs on tabular benchmark such as TabArena. |
| TFM prior              | The synthetic data generator being used for                            |

## Related work



There is little literature on designing TFM priors and isolating the effects of parts of the prior on downstream performance.
- Mitra [[@zhangMitraMixedSynthetic2025]] compares variations of trees and TabPFNv1 on downstream performance, diversity and distinctiveness of priors.
- [[@turkmenEvaluatingDataPriors2026]] compares open-source priors of a few models (TabICLv1, TICL, TabForest, TabPFN-1 and real data). For some priors, very high-level settings are varied. Furthermore, data similarity between the priors is explored.
- O'Prior [[@bouadiShapingPriorHow2026]] [[@bouadiWhatYouPretrain2026]] is a compositional prior built around four coupled components including a SCM meta generator and a realism engine.
We continue the objective of these works by treating prior design as the primary objective.

We make a distinction between _architecture-induced_ versus _graph-generated_ DAGs.^[These term are not final, should look for better words] 
Architecture-induced approaches specify the DAG and the functional relationships with the same mechanism. Typical examples are MLPs —where the DAG is induced from the dropout, number and width of the layers, and the functional relationships from its non-linearities— and trees such.
All earlier TFMs use this approach: 
- TabPFN-1 [[@hollmannTabPFNTransformerThat2023]] (MLPs), 
- TabICLv1 [[@quTabICLTabularFoundation2025]] (MLPs and XGBoost), 
- TICL [[@mullerMotherNetFastTraining]] (should look into this one) and 
- TabForestPFN [[@breejenFinetunedInContextLearning2025]] (should look into this one).
More recent TFMs use graph-generated DAGs (see table). Such DAGs are generated from a explicitly specified graph distribution. The functional relationships are independently specified from the DAG structure.

Closed-source priors do not share the code for the prior, but sometimes do describe the prior:
- TabPFN-2 [[@hollmannAccuratePredictionsSmall2025]] describes the prior to some technical detail (see table), although several components remain underspecified. For example, the edge probability $p_{\text{edge}}$ is reportedly sampled from a Gamma distribution. Because a Gamma distribution has support on $(0,\infty)$, it cannot directly define a probability in $[0,1]$ without an additional truncation or transformation that is not specified. See Appendix X for further discussion.
- TabPFN-3 [[@grinsztajnTabPFN3TechnicalReport2026]] does not provide technical details of its prior, but it does distinguish between, and shows examples of, sampling the DAG and sampling functional mechanisms.
- LimiX [[@zhangLimiXUnleashingStructuredData2025]] makes the same distinction. It specifies that DAGs are sampled through hierarchical generation based on local causal structures, while functional mechanisms include MLPs, convolutional layers, and decision trees.
- TabPFN-2.5 [[@grinsztajnTabPFN25AdvancingState2026]] and TabFM [[@googleIntroducingTabFMZeroshot2026]] specify little details on their DAG sampling approach.
So while we do not have technical details from closed-source priors, most priors hint to a graph-generated DAG sampling approach.

We do not consider real-world (i.e., non-synthetic) priors such as Real-TabPFN [[@gargRealTabPFNImprovingTabular2025]] and TabDPT [[@maTabDPTScalingTabular2025]].




| Model     | Reference                                 | Open-source | DAG type    | $p_\text{edge}$               | $n_\text{nodes}$                | $n_\text{features}$                       |
| --------- | ----------------------------------------- | ----------- | ----------- | ----------------------------- | ------------------------------- | ----------------------------------------- |
| GCFM      | [[@reuterUseWhatYou2026]]                 | ✅          | Erdös-Rényi | $\text{Beta}(2, 3)$           | $\mathcal{U}(2, 52)$            | equal to nodes                            |
| TabICL-v2 | [[@quTabICLv2BetterFaster2026]]           | ✅          | Cauchy      | N/A for Cauchy                | $\text{log-}\mathcal{U}(2, 32)$ | $\mathcal{U}(2, 100)$                     |
| TabPFN-v2 | [[@hollmannAccuratePredictionsSmall2025]] | ❌          | GNR         | $\text{Gamma}(\alpha, \beta)$ | $\text{log-}\mathcal{U}(a, b)$  | $\text{Beta}(0.95, 8)$, scaled to $1-160$ |
