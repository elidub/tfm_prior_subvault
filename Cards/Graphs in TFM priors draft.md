
## Literature
## Definitions and abbreviations


| Term                   | Explanation                                                            |
| ---------------------- | ---------------------------------------------------------------------- |
| Downstream performance | Performance of pre-trained TFMs on tabular benchmark such as TabArena. |
| TFM prior              | The synthetic data generator being used for                            |

| Abbreviation | Full                    |
| ------------ | ----------------------- |
| DTs          | Decision Trees          |
| MLPs         | Multi Layer Perceptrons |


## Related work



There is little literature on designing TFM priors and isolating the effects of parts of the prior on downstream performance.
- Mitra [[@zhangMitraMixedSynthetic2025]] compares variations of DTs and TabPFNv1 on downstream performance, diversity and distinctiveness of priors.
- [[@turkmenEvaluatingDataPriors2026]] compares open-source priors of a few models (TabICLv1, TICL, TabForest, TabPFN-1 and real data). For some priors, very high-level settings are varied. Furthermore, data similarity between the priors is explored.
- [[@daviesMindGapDistributional2026]] compares the distributions of web-scraped, curated real-world and TabICL-generated pre-training data. It finds that the TabICL prior covers a narrow region of the real-table distribution that remains distinguishable after extensive hyperparameter optimization, but does not find a clear relationship between distributional proximity and downstream performance.
- O'Prior [[@bouadiShapingPriorHow2026]] [[@bouadiWhatYouPretrain2026]] is a compositional prior built around four coupled components including a SCM meta generator and a realism engine. \[Should explain more but it's an architecture-induced prior including  MLPs, DTs \]
We continue the objective of these works by treating prior design as the primary objective.

We make a distinction between _architecture-induced_ versus _graph-generated_ DAGs.^[These term are not final, should look for better words] 
Architecture-induced approaches specify the DAG and the functional relationships with the same mechanism. Typical examples are MLPs —where the DAG is induced from the dropout, number and width of the layers, and the functional relationships from its non-linearities— and DTs such.
All earlier TFMs use this approach: 
- TabPFN-1 [[@hollmannTabPFNTransformerThat2023]] (MLPs), 
- TabICLv1 [[@quTabICLTabularFoundation2025]] (MLPs and XGBoost), 
- TICL [[@mullerMotherNetFastTraining]] (should look into this one) and 
- TabForestPFN [[@breejenFinetunedInContextLearning2025]] (should look into this one).
More recent TFMs use graph-generated DAGs (Table 1). Such DAGs are generated from a explicitly specified graph distribution. The functional relationships are independently specified from the DAG structure.

Closed-source priors do not share the code for the prior, but sometimes do describe the prior:
- TabPFN-2 [[@hollmannAccuratePredictionsSmall2025]] describes the prior to some technical detail (see table), although several components remain underspecified. For example, the redirection probability is reportedly sampled from a Gamma distribution. Because a Gamma distribution has support on $(0,\infty)$, it cannot directly define a probability in $[0,1]$ without an additional truncation or transformation that is not specified. See Appendix X for further discussion.
- TabPFN-3 [[@grinsztajnTabPFN3TechnicalReport2026]] does not provide technical details of its prior, but it does distinguish between, and shows examples of, sampling the DAG and sampling functional mechanisms.
- LimiX [[@zhangLimiXUnleashingStructuredData2025]] makes the same distinction. It specifies that DAGs are sampled through hierarchical generation based on local causal structures, while functional mechanisms include MLPs, convolutional layers, and DTs
- TabPFN-2.5 [[@grinsztajnTabPFN25AdvancingState2026]] and TabFM [[@googleIntroducingTabFMZeroshot2026]] specify little details on their DAG sampling approach.
So while we do not have technical details from closed-source priors, most priors hint to a graph-generated DAG sampling approach.

The following should probably be incorporated and condensed in the text above:
- APT [[@wuZeroshotMetalearningTabular2025]]jointly trains a TFM with adversarial synthetic data generators that adapt their MLP-based data-generating mechanisms to produce tasks on which the model performs poorly. It finds that these adaptive generators produce more diverse datasets than TabPFN’s fixed random generator, although architectural changes prevent isolating the contribution of the prior.
- RTFM [[@peroniRobustTabularFoundation2025]] searches the parameter space of an MLP-based SCM prior for synthetic tasks on which a TFM underperforms strong tree-based learners, and shifts continued pre-training toward these regions. This treats prior sampling as an adaptive robustness objective rather than analyzing individual prior components.
- TabICLv2 [[@quTabICLv2BetterFaster2026]] finds that replacing its synthetic prior produces the largest effect in its model-level ablation, while cross-combinations reveal a strong interaction between prior and architecture. However, individual components of the prior, including its graph-sampling choices, are not ablated.

As we focus on the synthetic data generator of single tables, we exclude related subfields.
- Real-world data priors (Real-TabPFN [[@gargRealTabPFNImprovingTabular2025]]; TabDPT [[@maTabDPTScalingTabular2025]]) do not model a DAG.
- Temporal and time-series PFN priors, including Drift-Resilient TabPFN [[@helliDriftResilientTabPFNInContext2024]], ForecastPFN [[@dooleyForecastPFNSyntheticallyTrained2023]], TimePFN [[@tagaTimePFNEffectiveMultivariate2025]], ApolloPFN [[@potapczynskiTimeAwarePriorFitted2026]], and CausalTimePrior [[@thummInterventionalTimeSeriesPriors2026]], model evolving data-generating mechanisms or sequential dependencies rather than the DAG of a static single table.
- PFNs on the instance-graph domain (GraphPFN [[@eremeevGraphPFNPriorDataFitted2026]]; NodePFN [[@choiLearningPosteriorPredictive2026]]) where a subset of the nodes in the graph represent instances (rows) instead of features in the graph.
- Multi-task learning
- Multi-table
(This paragraph can probably be moved to future work section at the end of the paper)

Table 1

| Prior          |                                           |                 | Graph params |                               |                                 | Dataset params                            |                     |                     | Training params   |
| -------------- | ----------------------------------------- | --------------- | ------------ | ----------------------------- | ------------------------------- | ----------------------------------------- | ------------------- | ------------------- | ----------------- |
| **Model name** | **Reference**                             | **Open-source** | **DAG type** | **$p_\text{edge}$**           | **$n_\text{nodes}$**            | **$n_\text{features}$**                   | $n_\text{rows max}$ | $n_\text{datasets}$ | $n_\text{params}$ |
| CFM            | [[@reuterUseWhatYou2026]]                 | ✅              | Erdös-Rényi  | $\text{Beta}(2, 3)$           | $\mathcal{U}(2, 52)$            | $n_\text{nodes} - 1$                      | 1000                | 1.6M                |                   |
| TabICLv2       | [[@quTabICLv2BetterFaster2026]]           | ✅              | Cauchy       | N/A for Cauchy                | $\text{log-}\mathcal{U}(2, 32)$ | $\mathcal{U}(2, 100)$                     | 60K                 | 35M                 | 27.6M             |
| TabPFN-2       | [[@hollmannAccuratePredictionsSmall2025]] | ❌              | GNR          | $\text{Gamma}(\alpha, \beta)$ | $\text{log-}\mathcal{U}(a, b)$  | $\text{Beta}(0.95, 8)$, scaled to $1-160$ | 10K                 | ~100M               | 7.2M              |

