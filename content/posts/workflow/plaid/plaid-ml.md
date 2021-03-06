---
title: PlaidML
date: 2022-04-08
author: Kyle McLester
summary: Train machine learning models on non-Nvidia GPU's
---

PlaidML allows you to leverage your systems discrete graphics card for training machine learning models -- even if you don't have an NVIDIA gpu.

## Installation

To install PlaidML and Keras, run the following:

```bash
python3 -m pip install -U plaidml-keras
```

Setup PlaidML to use your preferred computing device:

```bash
plaidml-setup
```

You will then be prompted the following items:

1. Enable Experimental Support
2. Choose Default Device
3. Save Settings to Path

Below is the expected output -- yours may look different as it depends on your system configuration:

![Plaid ML Setup](/posts/workflow/plaid/plaid-setup.png)

Full documentation can be [found here](https://plaidml.github.io/plaidml/docs/install)

## Run Initial Benchmark (opt)

Install and run the benchmark with the following:

```bash
python3 -m pip install plaidml-keras plaidbench
plaidbench keras resnet50
```

## Using PlaidML

The only difference to your workflow when using PlaidML, is that you must set your keras backend environment variable to use PlaidML. Something to also keep in mind is that you must use pure keras -- you lose tensorflow command access with this method.

In your import statements, you must set the enivronment variable:

```python
import os
import time
import numpy as np

os.environ["KERAS_BACKEND"] = "plaidml.keras.backend"

import keras
import keras.applications as kapp
from keras.datasets import cifar10
```

The following uses the [VGG-19 convolutional neural network](https://www.mathworks.com/help/deeplearning/ref/vgg19.html;jsessionid=b1c3289ab6ea0b8df0edbaf1846e). It is 19 layers deep and trained on more than 1 million images. The primary purpose of this model is for image classification. This code times how long it takes to classify 10 images.

```python
(x_train, y_train_cats), (x_test, y_test_cats) = cifar10.load_data()
batch_size = 8
x_train = x_train[:batch_size]
x_train = np.repeat(np.repeat(x_train, 7, axis=1), 7, axis=2)
model = kapp.VGG19()
model.compile(optimizer='sgd', loss='categorical_crossentropy',
              metrics=['accuracy'])

print("Running initial batch (compiling tile program)")
y = model.predict(x=x_train, batch_size=batch_size)

# Now start the clock and run 10 batches
print("Timing inference...")
start = time.time()
for i in range(10):
    y = model.predict(x=x_train, batch_size=batch_size)
print("Ran in {} seconds".format(time.time() - start))
```

```bash
INFO:plaidml:Opening device "metal_amd_radeon_pro_5300m.0"
Running initial batch (compiling tile program)
Timing inference...
Ran in 3.2314560413360596 seconds
```
