# Sampler

<!-- TOC -->

- [Sampler](#sampler)
    - [Overview](#overview)
    - [MindSpore Samplers](#mindspore-samplers)
        - [RandomSampler](#randomsampler)
        - [WeightedRandomSampler](#weightedrandomsampler)
        - [SubsetRandomSampler](#subsetrandomsampler)
        - [PKSampler](#pksampler)
        - [DistributedSampler](#distributedsampler)
    - [User-defined Sampler](#user-defined-sampler)

<!-- /TOC -->

<a href="https://gitee.com/mindspore/docs/blob/master/docs/programming_guide/source_en/sampler.md" target="_blank"><img src="./_static/logo_source.png"></a>

## Overview

MindSpore provides multiple samplers to help you sample datasets for various purposes to meet training requirements and solve problems such as oversized datasets and uneven distribution of sample categories. You only need to import the sampler object when loading the dataset to implement data sampling.

MindSpore provides the following samplers. In addition, you can define samplers as required.

| Sampler | Description |
| ----  | ----           |
| SequentialSampler | Sequential sampler, which samples a specified amount of data in the original data sequence.  |
| RandomSampler | Random sampler, which randomly samples a specified amount of data from a dataset.  |
| WeightedRandomSampler | Weighted random sampler, which randomly samples a specified amount of data from each category based on the specified probability.  |
| SubsetRandomSampler | Subset random sampler, which randomly samples a specified amount of data within a specified index range.  |
| PKSampler | PK sampler, which samples K pieces of data from each category in the specified dataset P.  |
| DistributedSampler | Distributed sampler, which samples dataset shards in distributed training.  |

## MindSpore Samplers

The following uses the CIFAR-10 as an example to introduce several common MindSpore samplers. Download [CIFAR-10 dataset](https://www.cs.toronto.edu/~kriz/cifar-10-binary.tar.gz) and unzip it, the directory structure is as follows:

```text
└─cifar-10-batches-bin
    ├── batches.meta.txt
    ├── data_batch_1.bin
    ├── data_batch_2.bin
    ├── data_batch_3.bin
    ├── data_batch_4.bin
    ├── data_batch_5.bin
    ├── readme.html
    └── test_batch.bin
```

### RandomSampler

Randomly samples a specified amount of data from the index sequence.

The following example uses a random sampler to randomly sample five pieces of data from the CIFAR-10 dataset with and without replacement, and displays shapes and labels of the loaded data.

```python
import mindspore.dataset as ds

ds.config.set_seed(0)

DATA_DIR = "cifar-10-batches-bin/"

sampler = ds.RandomSampler(num_samples=5)
dataset1 = ds.Cifar10Dataset(DATA_DIR, sampler=sampler)

for data in dataset1.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])

print("------------")

sampler = ds.RandomSampler(replacement=True, num_samples=5)
dataset2 = ds.Cifar10Dataset(DATA_DIR, sampler=sampler)

for data in dataset2.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])
```

The output is as follows:

```text
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 7
Image shape: (32, 32, 3) , Label: 0
Image shape: (32, 32, 3) , Label: 4
------------
Image shape: (32, 32, 3) , Label: 4
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 5
```

### WeightedRandomSampler

Specifies the sampling probability of each category and randomly samples a specified amount of data from each category based on the probability.

The following example uses a weighted random sampler to obtain six samples by probability from 10 categories in the CIFAR-10 dataset, and displays shapes and labels of the read data.

```python
import mindspore.dataset as ds

ds.config.set_seed(1)

DATA_DIR = "cifar-10-batches-bin/"

weights = [1, 1, 0, 0, 0, 0, 0, 0, 0, 0]
sampler = ds.WeightedRandomSampler(weights, num_samples=6)
dataset = ds.Cifar10Dataset(DATA_DIR, sampler=sampler)

for data in dataset.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])
```

The output is as follows:

```text
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 6
```

### SubsetRandomSampler

Randomly samples a specified amount of data from the specified index subset.

The following example uses a subset random sampler to obtain three samples from the specified subset in the CIFAR-10 dataset, and displays shapes and labels of the read data.

```python
import mindspore.dataset as ds

ds.config.set_seed(2)

DATA_DIR = "cifar-10-batches-bin/"

indices = [0, 1, 2, 3, 4, 5]
sampler = ds.SubsetRandomSampler(indices, num_samples=3)
dataset = ds.Cifar10Dataset(DATA_DIR, sampler=sampler)

for data in dataset.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])
```

The output is as follows:

```text
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 4
```

### PKSampler

Samples K pieces of data from each category in the specified dataset P.

The following example uses the PK sampler to obtain 2 samples (up to 20 samples) from each category in the CIFAR-10 dataset, and displays shapes and labels of the read data.

```python
import mindspore.dataset as ds

ds.config.set_seed(3)

DATA_DIR = "cifar-10-batches-bin/"

sampler = ds.PKSampler(num_val=2, class_column='label', num_samples=20)
dataset = ds.Cifar10Dataset(DATA_DIR, sampler=sampler)

for data in dataset.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])
```

The output is as follows:

```text
Image shape: (32, 32, 3) , Label: 0
Image shape: (32, 32, 3) , Label: 0
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 2
Image shape: (32, 32, 3) , Label: 2
Image shape: (32, 32, 3) , Label: 3
Image shape: (32, 32, 3) , Label: 3
Image shape: (32, 32, 3) , Label: 4
Image shape: (32, 32, 3) , Label: 4
Image shape: (32, 32, 3) , Label: 5
Image shape: (32, 32, 3) , Label: 5
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 7
Image shape: (32, 32, 3) , Label: 7
Image shape: (32, 32, 3) , Label: 8
Image shape: (32, 32, 3) , Label: 8
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 9
```

### DistributedSampler

Samples dataset shards in distributed training.

The following example uses a distributed sampler to divide a built dataset into three shards, obtains three data samples in each shard, and displays the read data.

```python
import numpy as np
import mindspore.dataset as ds

data_source = [0, 1, 2, 3, 4, 5, 6, 7, 8]

sampler = ds.DistributedSampler(num_shards=3, shard_id=0, shuffle=False, num_samples=3)
dataset = ds.NumpySlicesDataset(data_source, column_names=["data"], sampler=sampler)

for data in dataset.create_dict_iterator():
    print(data)
```

The output is as follows:

```text
{'data': Tensor(shape=[], dtype=Int64, value= 0)}
{'data': Tensor(shape=[], dtype=Int64, value= 3)}
{'data': Tensor(shape=[], dtype=Int64, value= 6)}
```

## User-defined Sampler

You can inherit the `Sampler` base class and define the sampling mode of the sampler by implementing the `__iter__` method.

The following example defines a sampler with an interval of 2 samples from subscript 0 to subscript 9, applies the sampler to the CIFAR-10 dataset, and displays shapes and labels of the read data.

```python
import mindspore.dataset as ds

class MySampler(ds.Sampler):
    def __iter__(self):
        for i in range(0, 10, 2):
            yield i

DATA_DIR = "cifar-10-batches-bin/"

dataset = ds.Cifar10Dataset(DATA_DIR, sampler=MySampler())

for data in dataset.create_dict_iterator():
    print("Image shape:", data['image'].shape, ", Label:", data['label'])
```

The output is as follows:

```text
Image shape: (32, 32, 3) , Label: 6
Image shape: (32, 32, 3) , Label: 9
Image shape: (32, 32, 3) , Label: 1
Image shape: (32, 32, 3) , Label: 2
Image shape: (32, 32, 3) , Label: 8
```