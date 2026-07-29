## Claude Opus 
I'll read the draft.

Read the draft and the surrounding notes ([First literature review](https://claude.ai/epitaxy/Shared/tfm_prior_subvault/Cards/First%20literature%20review.md), the [team meeting](https://claude.ai/epitaxy/Shared/tfm_prior_subvault/Calender/Meetings/26-07-22%20-%20TFM%20prior%20team.md)). The framing (prior design as the primary object, architecture-induced vs graph-generated) is good and the Table-1 survey is a real contribution. The method section is where I'd push back hardest — it's currently a factorial sweep without a stated estimand, and several factors are confounded by construction.

### The three things I'd fix before running anything

**1. The DAG-family comparison is confounded with density, and you already know it.**

You note GNR's sparsity "can not be controlled for" and then proceed to compare it against ER at Beta(6,2) (mean density 0.75). GNR-with-redirection (Krapivsky–Redner) is a _growing tree_: each new node attaches with exactly one edge, so |E| = n−1 and every node has in-degree (or out-degree, depending on orientation) exactly 1. Two consequences:

- Any GNR-vs-ER difference is a density difference of an order of magnitude, not a topology difference. The "graph family" effect is not identified.
- More importantly for the science: if GNR yields trees, **every functional mechanism in the SCM is univariate** — the true Data Generation Process (DGP) contains no feature interactions at all. That is a far bigger difference than hubness, and it plausibly dominates everything else you measure. TabPFN-2 almost certainly doesn't use a bare GNR tree (some densification step is unspecified), which is worth saying in the paper.

Fix: parameterize every family by a _common realized statistic_, not by its native knob. Pick target expected-edges-per-node (the standard in the causal-discovery literature — ER-1/ER-2/ER-4, SF-1/SF-2/SF-4 — precisely so families are comparable), calibrate each family's native parameter to hit it, and let the family effect be the residual. Then GNR enters at its natural degree and you add ER/Cauchy arms matched to _that_ degree, so hubness is compared at equal sparsity.

**2. `p_edge` at a fixed value confounds density with `n_nodes`.**

In ER, fixed p gives expected degree ∝ n. So your four node buckets (2–3, 4–7, 8–15, 16–32) are simultaneously a size manipulation and a density manipulation — an 8× density swing across buckets. Since one of your stated goals is "what graph settings are useful for larger/smaller graphs," this is exactly the interaction you can't read off the current design. Expected-degree parameterization fixes this too.

Also check whether your Cauchy levels do anything: with A, B_i, C_j standard Cauchy, the sigmoid argument has infinite variance and p_ij is close to bimodal at {0,1}; a bias shift of ±3 may move realized density far less than ER's Beta(2,6)→Beta(6,2) (0.25→0.75). If so, "3 levels of p_edge" is a wildly unequal manipulation across families and the family main effect will absorb it. Your own `summary_density.png` should already show this — worth reading it as a design check, not just as a descriptive figure.

**3. You're varying location but not spread, and spread is the more interesting hypothesis.**

For ER you sample p_edge ~ Beta _per dataset_ (so the prior has structural diversity); for Cauchy p_edge is a fixed constant. So "family" also confounds "distribution over sparsity" vs "single sparsity." Given that Mitra and APT both argue diversity is the active ingredient, I'd make this an explicit factor rather than a nuisance: at matched mean density, compare a point-mass p_edge against a Beta-distributed p_edge. That's a cheap arm and it's a _mechanistic_ result — the kind of "why" you say the categorical sweep won't give you.

### Design gaps

**The functional mechanism is never specified.** The whole premise of graph-generated priors is that structure and mechanism are decoupled — so the paper needs to state exactly what's held fixed: mechanism family (MLP/DT/mixed), noise model, activation sampling, post-processing (warping, discretization, missingness), and crucially **how features and the target are selected from nodes**. With n_nodes up to 32 and n_features_max ∈ {3, 5, 10}, you're discarding most nodes; whether the target is a sink, a root, or mid-graph, and whether dropped nodes are marginalized-out confounders, will swamp the DAG-family effect. This is a first-order design decision currently left implicit.

Related, the constraint is inconsistent as written: bucket log-U(2,3) gives 2–3 nodes while n_features_max goes to 10. Define n_features ≤ n_nodes − 1 (CFM's convention) or state the sampler's behaviour.

**No evaluation protocol.** "Downstream performance on TabArena" won't survive contact with a 3.7M-param model trained on 10K datasets of 256 rows and ≤10 features. Two problems: (a) benchmark datasets are far outside the training regime, so OOD generalization dominates the graph effect you're trying to measure; (b) the ranking may be pure noise. I'd specify a regime-matched benchmark subset (small n, few features) as the primary outcome, plus — and I think this is the stronger idea — a **cross-prior transfer matrix**: train on family A, evaluate on held-out synthetic data from families A…E. That separates "prior intrinsically induces a better learner" from "prior happens to match the benchmark," it's cheap, and it gives you the mechanism story.

**No seeds, no power analysis, no compute budget.** 5 families × 3 p_edge × 4 node buckets × 3 feature caps × 3 model sizes = 540 runs at one seed each. Between-seed variance in downstream TFM performance at this scale is routinely larger than between-prior differences. Before committing: run a pilot of the two most extreme settings × 3–5 seeds, measure seed variance, and size the grid off that. Very likely you should shrink the factorial (fix model size for the main grid) and spend the saved compute on replicates.

**The scaling-law claim is the weakest link and a reviewer will go straight at it.** "we hypothesize with scaling laws that the interpretations will hold at industry-scale" is an assertion, not evidence. Make it a testable secondary hypothesis — _rank stability of the DAG settings across scale_ — and note that your three model sizes span 1.2M–10.4M, less than one order of magnitude, while `n_datasets` is **fixed at 10K**, four orders below the real priors. Prior _diversity_ is exactly the kind of factor whose effect appears only with training scale (a narrow prior is fine at 10K datasets and saturates at 100M) — so the direction of the effect could plausibly reverse. If you can only afford one scaling axis, I'd vary `n_datasets` (10K/40K/160K) over model size: it's the axis on which the extrapolation claim actually rests.

**Missing baselines.** Add (a) an architecture-induced MLP prior (TabPFN-1 style) as the reference point the field is coming from, and (b) a degenerate control — empty graph / independent features, or fully connected. Without these you have relative rankings but no sense of whether the whole DAG-family effect is 0.5% or 15% of achievable performance.

**No stated hypotheses or analysis plan.** Worth writing down before running: primary hypothesis, primary outcome, and the statistical model (mixed-effects with random effects per benchmark dataset, effect size reported against seed variance). The "why" question you raise on line 79 is better answered by regressing performance on _realized_ structural statistics — density, in/out-degree Gini, longest path, number of roots/sinks — pooled across all families, than by any categorical family comparison. You already compute two of these; treat them as regressors, not just figures.

### Smaller

- "We evaluate all known DAG generation approaches in the literature" — soften, or add Barabási–Albert / scale-free proper (GNR is one route to it, and SF is the standard ER-counterpart in causal-discovery benchmarks). LimiX's hierarchical local structures and O'Prior's SCM meta-generator are also DAG samplers you're excluding.
- Table 1 says TabICLv2 `p_edge` is "N/A for Cauchy"; the method treats `p_edge = 0` as the bias term. Make consistent.
- Your Gamma-support critique of TabPFN-2 is a nice observation, but "redirection probability cannot directly define a probability in [0,1]" reads as if it's your fix's justification — state the alternatives you rejected (truncation, 1−exp(−x)) so the Beta substitution looks like a choice rather than the only option.
- Unfinished/typo: line 28 "and DTs such."; line 80 "very some of them" → "vary"; line 84 sentence stops mid-clause; line 88 "number of nodes $n_\text{features} = 3$".
- Erdős–Rényi spelling (and the `erdds1959random` cite key).

If you want, I can restructure the Method section around the expected-degree parameterization + hypotheses/analysis-plan framing and write it into the file.

## Codex Sol

Short answer: the research question is strong, but the current plan would not isolate “the effect of the DAG sampler.” It would compare bundles of density, connectivity, hubness, orientation, and feature–target reachability.

### Critical issues

1. **Compare the effective graph, not the initially sampled DAG.**  
    In TabICLv2, observed columns and the target are assigned to nodes, after which unused nodes are pruned. With 3–10 observed features but up to 32 latent nodes, much of the sampled graph may never affect the table. Measure the post-pruning graph, particularly:
    
    - target ancestors and descendants;
    - number of observed features connected to the target;
    - feature–target path lengths;
    - roots, sinks, components and v-structures;
    - effective depth, density and degree concentration.
    
    Otherwise an apparent sampler effect may actually be a target-placement effect. This follows directly from the [TabICLv2 full PDF](/Users/elias/Zotero/storage/W6TYIILT/Qu e.a. - 2026 - TabICLv2 A better, faster, scalable, and open tabular foundation model.pdf).
    
2. **The graph families are heavily confounded.**  
    In the proposed comparison ([draft lines 67–78 (line 67)](/Users/elias/Library/Mobile Documents/iCloud~md~obsidian/Documents/iCloud Synced Vault/Shared/tfm_prior_subvault/Cards/Graphs in TFM priors draft.md:67)):
    
    - ER can be disconnected and dense;
    - Cauchy varies global density and node-specific in/out propensity;
    - canonical GNR produces a connected tree.
    
    Hence “Cauchy beats GNR” cannot be attributed to hubness: it may be density, connectivity, depth, or number of paths. I would split this into:
    
    - A density/connectivity-matched ER-versus-Cauchy experiment;
    - A tree-only experiment comparing GNR with matched random-tree controls;
    - A separate orientation intervention on the same GNR skeleton.
1. **GNR’s parameter is not an edge parameter.**  
    Canonical GNR adds one edge with every new node, irrespective of redirection probability. Redirection controls preferential attachment, hub concentration and depth—not density. The converging orientation is the cited model; diverging and random orientations should be described as your interventions on a GNR skeleton. This is explicit in the [Krapivsky–Redner PDF](/Users/elias/Zotero/storage/NMX9A6CV/Krapivsky en Redner - 2001 - Organization of growing random networks.pdf).
    
2. **The experiment count and scaling claim do not line up.**  
    Crossing 15 graph settings × 4 node ranges × 3 feature ranges × 3 model sizes gives **540 training runs before seeds**. At the same time, three model sizes at one training budget do not establish a scaling law. Scaling could also change the ranking of priors.
    
    A better staged design:
    
    - calibrate graph distributions without training;
    - screen all graph settings at one model size with at least three seeds;
    - shortlist 3–5 priors;
    - scale only those across model size **and training budget**;
    - reserve held-out datasets for final confirmation.
3. **Ten thousand datasets is likely too small and is not a complete compute specification.**  
    The nanoTabPFN paper trains its example on 80,000 datasets and reports rapid convergence only in a closely matched 150-row, five-feature setting. Specify optimizer steps, batch size, number of unique datasets, repeated epochs, and total cells/tokens processed—not merely `n_datasets`. Use pilot learning curves to establish that every treatment has reached a comparable stage of convergence. See the [nanoTabPFN PDF](/Users/elias/Zotero/storage/RIN56446/Pfefferle e.a. - 2025 - nanoTabPFN A Lightweight and Educational Reimplementation of TabPFN.pdf).
    
4. **Fixed 256 rows and very few features limit the conclusion.**  
    The recent prior-evaluation paper finds that feature-to-sample ratio materially changes downstream performance. Your ratios range roughly from 0.008 to 0.039, and `n_features ≤ 3` leaves very little graph structure visible. Either include two row regimes or explicitly restrict the claim to low-dimensional, low-data classification. The [Türkmen et al. PDF](/Users/elias/Zotero/storage/D99KGDB7/Türkmen e.a. - 2026 - Towards Evaluating Data Priors for Tabular Foundation Models.pdf) supports this concern.
    

### Recommended primary design

I would define the primary estimand as:

> The effect of changing graph topology while approximately holding the distributions of dataset dimensions, structural mechanisms, signal-to-noise ratio, target placement, and selected effective-graph properties constant.

Then:

- Calibrate sampler parameters using post-pruning graphs rather than treating `−3/0/3` and the three Beta distributions as intrinsically comparable.
- Use common random numbers for dimensions, mechanisms, noise and target placement where possible.
- Normalize mechanism output variance/SNR across parent counts; otherwise denser graphs silently create different functional-complexity distributions.
- Include sampler-default baselines, an empty/independent graph control, and a mixture-over-graph-settings baseline.
- Predefine a primary downstream metric and a hierarchical analysis over benchmark datasets and training seeds, with confidence intervals and correction for the many contrasts.
- Keep a development benchmark separate from the final held-out benchmark to avoid selecting among hundreds of configurations on the reported test tasks.

One factual correction: Table 1 lists TabPFN-2’s maximum rows as 10K, but its full paper says pretraining sampled up to 2,048 training rows plus 128 validation rows; 10K is closer to its stated inference regime. See the [TabPFN-2 PDF](/Users/elias/Zotero/storage/J4KW2VTB/Hollmann e.a. - 2025 - Accurate predictions on small data with a tabular foundation model.pdf).

My strongest recommendation is to make **target-relative effective topology** the centerpiece. Density and degree Gini of the raw DAG are useful generator diagnostics, but they are too remote from the prediction problem to explain downstream performance reliably. I accessed the cited local full-text PDFs through Zotero and made no library or draft changes.