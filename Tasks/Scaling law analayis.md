---
tags:
creation date: 26-08-17
assigned:
category:
  - after training
status: backlog
---
- [ ] Read up about [neural scaling laws](https://en.wikipedia.org/wiki/Neural_scaling_law) and some papers



- Similar to 
	- Figure 16 of [[@grinsztajnTabPFN3TechnicalReport2026]]
	- Figure 1 of [[@maTabDPTScalingTabular2025]]
![[Scaling law analayis 2026-08-18 10.08.21.excalidraw]]


- Number of parameters in model (in sketch on x axis) and number of datasets in prior (as colour in legend) 
	- could also be swapped,
	- could also be multiplied to get a single metric on the x-axis (like they sometimes do in these neural scaling laws plots)
- Normalized score could be any of the aggregating metrics from TabArena such as normalized ROC AUC, improvability, ELO, rank


- Hopefully we can say something like:
	- "The benefit of best performing DAG type over the other DAG types" (whatever that may be) "stays consistent as we increase in model or dataset size"