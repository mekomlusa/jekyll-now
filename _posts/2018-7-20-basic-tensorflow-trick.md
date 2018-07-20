---
layout: post
title: Basic Tensorflow/Deep Learning Tricks
---

I'm sure that most people already know, but I didn't know when assigned to my first real-world deep learning project :( I guess it's better keep the tricks somewhere now so that it will be easier for me to refer to in the future!

* To check GPU usage: $`nvidia-smi`
* To use only limited GPUs (say the 3 and 4 in this case):
```python
import os
os.environ["CUDA_VISIBLE_DEVICES"]="2,3"
```
* If the validation accuracy becomes stable after several epochs, and the results are not promising, check and see if you have the **learning rate** set too high.
