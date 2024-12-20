# RepVGG

<!--- Guideline: please use url linked to the paper abstract in ArXiv instead of PDF for fast loading.  -->
> [RepVGG: Making VGG-style ConvNets Great Again](https://arxiv.org/abs/2101.03697)

## Introduction

<!--- Guideline: Introduce the model and architectures. Please cite if you use/adopt paper explanation from others. -->
<!--- Guideline: If an architecture table/figure is available in the paper, please put one here and cite for intuitive illustration. -->

The key idea of Repvgg is that by using re-parameterization, the model architecture could be trained in multi-branch but
validated in single branch.
Figure 1 shows the basic model architecture of Repvgg. By utilizing different values for a and b, we could get various
repvgg models.
Repvgg could achieve better model performance with smaller model parameters on ImageNet-1K dataset compared with
previous methods.[[1](#references)]

<p align="center">
  <img src="https://user-images.githubusercontent.com/77485245/226785860-e109b0ea-eb6b-4464-91a9-8898b3e3e056.png" width=400 />
</p>
<p align="center">
  <em>Figure 1. Architecture of Repvgg [<a href="#references">1</a>] </em>
</p>

## Results

<!--- Guideline:
Table Format:
- Model: model name in lower case with _ seperator.
- Context: Training context denoted as {device}x{pieces}-{MS mode}, where mindspore mode can be G - graph mode or F - pynative mode with ms function. For example, D910x8-G is for training on 8 pieces of Ascend 910 NPU using graph mode.
- Top-1 and Top-5: Keep 2 digits after the decimal point.
- Params (M): # of model parameters in millions (10^6). Keep 2 digits after the decimal point
- Recipe: Training recipe/configuration linked to a yaml config file. Use absolute url path.
- Download: url of the pretrained model weights. Use absolute url path.
-->

Our reproduced model performance on ImageNet-1K is reported as follows.

performance tested on ascend 910*(8p) with graph mode

<div align="center">

|   Model   | Top-1 (%) | Top-5 (%) | ms/step | Params (M) | Batch Size | Recipe                                                                                         | Download                                                                                              |
| :-------: | :-------: | :-------: | :-----: | :--------: | ---------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| repvgg_a0 |   72.29   |   90.78   |  25.14  |    9.13    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/repvgg/repvgg_a0_ascend.yaml) | [weights](https://download-mindspore.osinfra.cn/toolkits/mindcv/repvgg/repvgg_a0-b67a9f15-910v2.ckpt) |
| repvgg_a1 |   73.68   |   91.51   |  31.78  |   14.12    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/repvgg/repvgg_a1_ascend.yaml) | [weights](https://download-mindspore.osinfra.cn/toolkits/mindcv/repvgg/repvgg_a1-a40aa623-910v2.ckpt) |

</div>

performance tested on ascend 910(8p) with graph mode

<div align="center">

|   Model   | Top-1 (%) | Top-5 (%) | Params (M) | Batch Size | Recipe                                                                                         | Download                                                                                |
|:---------:|:---------:|:---------:|:----------:|------------|------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| repvgg_a0 |   72.19   |   90.75   |    9.13    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/repvgg/repvgg_a0_ascend.yaml) | [weights](https://download.mindspore.cn/toolkits/mindcv/repvgg/repvgg_a0-6e71139d.ckpt) |
| repvgg_a1 |   74.19   |   91.89   |   14.12    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/repvgg/repvgg_a1_ascend.yaml) | [weights](https://download.mindspore.cn/toolkits/mindcv/repvgg/repvgg_a1-539513ac.ckpt) |

</div>

#### Notes

- Top-1 and Top-5: Accuracy reported on the validation set of ImageNet-1K.

## Quick Start

### Preparation

#### Installation

Please refer to the [installation instruction](https://github.com/mindspore-lab/mindcv#installation) in MindCV.

#### Dataset Preparation

Please download the [ImageNet-1K](https://www.image-net.org/challenges/LSVRC/2012/index.php) dataset for model training
and validation.

### Training

<!--- Guideline: Please avoid using shell scripts in the command line. Python scripts preferred. -->

* Distributed Training

It is easy to reproduce the reported results with the pre-defined training recipe. For distributed training on multiple
Ascend 910 devices, please run

```shell
# distributed training on multiple GPU/Ascend devices
msrun --bind_core=True --worker_num 8 python train.py --config configs/repvgg/repvgg_a1_ascend.yaml --data_dir /path/to/imagenet
```


Similarly, you can train the model on multiple GPU devices with the above `msrun` command.

For detailed illustration of all hyper-parameters, please refer
to [config.py](https://github.com/mindspore-lab/mindcv/blob/main/config.py).

**Note:**  As the global batch size  (batch_size x num_devices) is an important hyper-parameter, it is recommended to
keep the global batch size unchanged for reproduction or adjust the learning rate linearly to a new global batch size.

* Standalone Training

If you want to train or finetune the model on a smaller dataset without distributed training, please run:

```shell
# standalone training on a CPU/GPU/Ascend device
python train.py --config configs/repvgg/repvgg_a1_ascend.yaml --data_dir /path/to/dataset --distribute False
```

### Validation

To validate the accuracy of the trained model, you can use `validate.py` and parse the checkpoint path
with `--ckpt_path`.

```
python validate.py -c configs/repvgg/repvgg_a1_ascend.yaml --data_dir /path/to/imagenet --ckpt_path /path/to/ckpt
```

### Deployment

Please refer to the [deployment tutorial](https://mindspore-lab.github.io/mindcv/tutorials/deployment/) in MindCV.

## References

<!--- Guideline: Citation format GB/T 7714 is suggested. -->

[1] Ding X, Zhang X, Ma N, et al. Repvgg: Making vgg-style convnets great again[C]//Proceedings of the IEEE/CVF
conference on computer vision and pattern recognition. 2021: 13733-13742.
