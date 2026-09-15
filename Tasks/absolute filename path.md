---
tags:
creation date: 26-08-19
assigned: elias
category:
  - during training
status:
  - merging
---
Currenlty running a toy expeirment with `cd gtfm/gtfm`, `python pretrain.py exp=dev`

```gtfm/jobs/configs/exp/dev.yaml
# @package _global_

prior:
  filename: ../data/cauchy_offset_0/nodesdiscrete_loguniform_low02_high32_cutoff_low08_cutoff_high15/maxfeat3/prior.h5
  num_steps: 2
  batch_size: 2

trainer:
  epochs: 2

logger:
  evaluation: toy
  tags:
    - dev2
```

However, maybe it's easier to be able to just set the filename with a datapath, such that we would have


```gtfm/jobs/configs/exp/dev.yaml
# @package _global_

prior:
  data_dir: ../data
  file_path: cauchy_offset_0/nodesdiscrete_loguniform_low02_high32_cutoff_low08_cutoff_high15/maxfeat3/
  file_name: prior.h5
	
...
```

and then we would do something like `self.filename: Path = data_dir / file_path / file_name`

or maybe it would be even better to define just through prior params (similar as in the generation) instead of the filename, but not that sure if that would increase the code quality.