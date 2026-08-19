---
tags:
creation date: 26-08-17
assigned:
category:
  - before training
status: Done
---
- Preprocessing TabArena datasets is currently done during downloading them in `download.py`.
	- [encode_features](https://github.com/elidub/TFM/blob/fd143d2ae81e9cd8b8ea7e99798b6dbffc409c4d/gtfm/gtfm/tabarena_eval/data.py#L268)
	- We do it there already to save compute
- This also happens in NanoTabPFNClassifier
	- [self.feature_preprocessor - get_feature_preprocessor](https://github.com/elidub/TFM-Playground/blob/384ad4b76bcc0086a60191463652729b3f3759db/tfmplayground/interface.py#L120)
- So we are preprocessing data twice, which shouldn't be necessary
	- It doesn't has a negative effect now as the second preprocessign doesn't do anything, [as tested](https://github.com/elidub/TFM/blob/fd143d2ae81e9cd8b8ea7e99798b6dbffc409c4d/gtfm/tests/test_tabarena_preprocessing.py#L8)
- But it is ugly code practice which can/should be solved.