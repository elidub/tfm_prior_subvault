---
status: open
creation date: 26-07-21
kanban_order: V2
---
Look at [TFM-Playground](https://github.com/automl/TFM-Playground/tree/main "https://github.com/automl/tfm-playground/tree/main") and reproduce a tiny 'reproduce-data → train-model' run, can be just a tiny dataset and a tiny model.

Re-opened to do (if already done, directly set it do done):
- [ ] Run `0_distributions.ipynb` to create the scm configs.
- [ ] Create a prior dataset by running from `cd gtfm/gtfm; python generate.py prior_generate=tabicl_v2 dataset_name=dev`
	- `prior_generate=tabicl_v2`  uses some pre-defined `scm/` configs
		- [ ] Now run them without selecting the `scm/` configs, but defining scm parameters in the `tabicl_v2.yaml` directly