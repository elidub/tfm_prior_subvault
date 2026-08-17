## Notes
- [[AI feedback]]
- [[Hyperparameter importance|hyperparameter importance]]
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

## Method

We evaluate all known DAG generation approaches in the literature (Table 1). Each DAG type is controlled by two hyperparameters,
$$\begin{aligned}
\text{DAG sampler} &= \{ \text{Erdös-Rényi} | \text{Cauchy} | \text{GNR} \}
\\
\mathcal{G} &\sim \text{DAG sampler} (n_\text{nodes}, p_\text{edge}) \end{aligned}$$
with the number of nodes $n_\text{nodes}$ and an edge parameter $p_\text{edge}$. The edge parameter controls how edges are distributed in the DAG and has a different meaning per DAG type.

- **Erdos-Renyi** [[@erdds1959random]] has edge probability for $p_\text{edge}$ which controls the sparsity of the DAG.  CFM [[@reuterUseWhatYou2026]] uses this with $p_\text{edge} \sim \text{Beta}(2, 3)$. In our experiments, we vary this for three different sparsities $p_\text{edge} \sim \text{Beta}(\alpha, \beta)$ with $(\alpha, \beta) = (2, 6), (6, 6), (6, 2)$.
- **Cauchy** DAGs include an edge for node indices $i < j$ with probability $$p_{ij}=\mathrm{sigmoid}(p_\text{edge} + A+B_i+C_j)$$where $A$, $B_i$, $C_j$ are independent standard Cauchy random variables. The edge parameter $p_\text{edge}$ controls for a bias in the sparsity.  TabICLv2 [[@quTabICLv2BetterFaster2026]] has $p_\text{edge} = 0$. In our experiments, we vary this with $p_\text{edge} = -3, 0, 3$
- **Growing network with redirection sampling (GNR)** [[@krapivskyOrganizationGrowingRandom2001]] has an redirection probability as $p_\text{edge}$. TabPFN-2 [[@hollmannAccuratePredictionsSmall2025]] specifies that is uses Gamma distribution for the redirection probability which specifies the how concentrated edges are around hubs. Because redirection probability cannot directly define a probability in $[0,1]$ (further discussion in Appendix X), we use the same Beta distributions as for Erdos-Renyi.  In GNRs, sparsity is directly dependent on $n_\text{nodes}$ so can not be controlled for. GNRs can be defined as converging (the default in the literature), diverging or random graphs. TabPFN-2 does not specify which variant it uses, we experiment with all three.

The above results in 15 graph settings: 5 DAG types (Erdos-Renyi, Cauchy, three variants of GNR) all with 3 edge parameter $p_\text{edge}$ settings (controlling for sparsity or hubness). To achieve state-of-the-art performance this would require training models with expensive dataset and training parameters (Table 1). 
Furthermore, this would not give us insight why certain graph settings perform good or bad. 
Instead, we keep values for the dataset and training parameters small, and vary some of them and hypothesize with scaling laws that the interpretations will hold at industry-scale TFMs. 

- **Number of nodes** $n_\text{nodes}$. We put the distribution of TabICLv2 in quantiles: $n_\text{nodes} \sim \text{log-}\mathcal{U}(a, b)$ with $(a, b) = (2, 3), (4, 7), (8, 15), (16, 32)$. This allows to understand what graph settings are useful for larger/smaller graphs.
- **Number of features** $n_\text{features}$. Following TabICLv2 with $n_\text{features} \sim \mathcal{U}(2, n_\text{feature max})$ but instead of having the maximum number of features $n_\text{feature max} = 100$, we vary $n_\text{feature max} = 3, 5, 10$.
- **Number of rows** $n_\text{rows}$ is fixed to $256$. We do not vary this as it would be a big driver of the increase for computational budget, and because we believe that graph setting and $n_\text{rows}$ 
- **Number of datasets** $n_\text{datasets}$ is fixed to 10K.
- **Number of parameters** $n_\text{params}$ we use the default setting of NanoTabPFN [[@pfefferleNanoTabPFNLightweightEducational2025]] with 3.7M parameters.  We add one smaller and larger model, respectively 1.2M and 10.4M

Resulting graphs examples and summary statistics (density, irn-degree Gini and out-degree Gini) from the 5 DAG types, 3 edge parameters, 4 number of node buckets with number of nodes $n_\text{features} = 3$ are shown in the figures below.

We will use the TabICLv2 prior for synthetic data generation. It's the most recent and open-source SOTA model. 
#### Graphs examples
- Arrowheads are not drawn, all edges are directed and point from left to right.
- blue nodes: features
- orange nodes: target
- black lines: functional relationships
- green lines: 'observations'. With 'observations' we refer to the arrows pointing from right to left in Figure 4c of [[@quTabICLv2BetterFaster2026]]. Consider this quote from the paper: "Only a subset of a node’s dimensions is used to generate each feature, leaving other dimensions unobserved and thereby introducing noise into the dataset." The 'subset of a node dimensions' is what we refer to as an observation.

![[IMG-20260729145823.png]]
![[IMG-20260729145823-1.png]]
![[IMG-20260729145823-2.png]]
![[IMG-20260729145823-3.png]]
![[IMG-20260729145823-4.png]]

#### Graph summary statistics
![[IMG-20260729145823-5.png]]
![[IMG-20260729145823-6.png]]
![[IMG-20260729145823-7.png]]
