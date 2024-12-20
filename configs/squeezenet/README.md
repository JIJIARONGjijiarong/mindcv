# SqueezeNet

> [SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and <0.5MB model size](https://arxiv.org/abs/1602.07360)

## Introduction

SqueezeNet is a smaller CNN architectures which is comprised mainly of Fire modules and it achieves AlexNet-level
accuracy on ImageNet with 50x fewer parameters. SqueezeNet can offer at least three advantages: (1) Smaller CNNs require
less communication across servers during distributed training. (2) Smaller CNNs require less bandwidth to export a new
model from the cloud to an autonomous car. (3) Smaller CNNs are more feasible to deploy on FPGAs and other hardware with
limited memory. Additionally, with model compression techniques, SqueezeNet is able to be compressed to less than
0.5MB (510× smaller than AlexNet). Blow is macroarchitectural view of SqueezeNet architecture. Left: SqueezeNet ;
Middle: SqueezeNet with simple bypass; Right: SqueezeNet with complex bypass.

<p align="center">
  <img src="https://user-images.githubusercontent.com/8156835/210043440-5218ae6d-54ed-4d1e-a4ff-335d4a2baaa5.png" width=800 />
</p>
<p align="center">
  <em>Figure 1. Architecture of SqueezeNet [<a href="#references">1</a>] </em>
</p>

## Results

Our reproduced model performance on ImageNet-1K is reported as follows.

performance tested on ascend 910*(8p) with graph mode

<div align="center">

|     Model     | Top-1 (%) | Top-5 (%) | ms/step | Params (M) | Batch Size | Recipe                                                                                                  | Download                                                                                                      |
| :-----------: | :-------: | :-------: | :-----: | :--------: | ---------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| squeezenet1_0 |   58.75   |   80.76   |  24.28  |    1.25    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/squeezenet/squeezenet_1.0_ascend.yaml) | [weights](https://download-mindspore.osinfra.cn/toolkits/mindcv/squeezenet/squeezenet1_0-24010b28-910v2.ckpt) |

</div>

performance tested on ascend 910(8p) with graph mode

<div align="center">

|     Model     | Top-1 (%) | Top-5 (%) | Params (M) | Batch Size | Recipe                                                                                                  | Download                                                                                        |
|:-------------:|:---------:|:---------:|:----------:|------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
| squeezenet1_0 |   58.67   |   80.61   |    1.25    | 32         | [yaml](https://github.com/mindspore-lab/mindcv/blob/main/configs/squeezenet/squeezenet_1.0_ascend.yaml) | [weights](https://download.mindspore.cn/toolkits/mindcv/squeezenet/squeezenet1_0-eb911778.ckpt) |

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

<!--- Guideline: Avoid using shell script in the command line. Python script preferred. -->

* Distributed Training

It is easy to reproduce the reported results with the pre-defined training recipe. For distributed training on multiple
Ascend 910 devices, please run

```shell
# distributed training on multiple GPU/Ascend devices
msrun --bind_core=True --worker_num 8 python train.py --config configs/squeezenet/squeezenet_1.0_ascend.yaml --data_dir /path/to/imagenet
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
python train.py --config configs/squeezenet/squeezenet_1.0_ascend.yaml --data_dir /path/to/dataset --distribute False
```

### Validation

To validate the accuracy of the trained model, you can use `validate.py` and parse the checkpoint path
with `--ckpt_path`.

```
python validate.py -c configs/squeezenet/squeezenet_1.0_ascend.yaml --data_dir /path/to/imagenet --ckpt_path /path/to/ckpt
```

### Deployment

To deploy online inference services with the trained model efficiently, please refer to
the [deployment tutorial](https://mindspore-lab.github.io/mindcv/tutorials/deployment/).

## References

<!--- Guideline: Citation format GB/T 7714 is suggested. -->
[1] Iandola F N, Han S, Moskewicz M W, et al. SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and< 0.5 MB
model size[J]. arXiv preprint arXiv:1602.07360, 2016.
