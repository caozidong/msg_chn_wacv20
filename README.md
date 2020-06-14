# msg_chn_wacv20
This repository contains the network configurations (in PyTorch) of our paper "[A Multi-Scale Guided Cascade Hourglass Network for Depth Completion](http://openaccess.thecvf.com/content_WACV_2020/papers/Li_A_Multi-Scale_Guided_Cascade_Hourglass_Network_for_Depth_Completion_WACV_2020_paper.pdf)" by Ang Li, Zejian Yuan, Yonggen Ling, Wanchao Chi, Shenghao Zhang and Chong Zhang.

## Introduction
Depth completion, a task to estimate the dense depth map from sparse measurement under the guidance from the
high-resolution image, is essential to many computer vision applications. Most previous methods building on fully convolutional networks can not handle diverse patterns in the depth map efficiently and effectively. We propose a multi-scale guided cascade hourglass network to tackle this problem. Structures at different levels are captured by specialized hourglasses in the cascade network with sparse inputs in various sizes. An encoder extracts multiscale features from color image to provide deep guidance
for all the hourglasses. A multi-scale training strategy further activates the effect of cascade stages. With the role of
each sub-module divided explicitly, we can implement components with simple architectures. Extensive experiments show that our lightweight model achieves competitive results compared with state-of-the-art in KITTI depth completion benchmark, with low complexity in run-time.

<p align="center">
  <img src="https://github.com/anglixjtu/msg_chn_wacv20/blob/master/demo/video5.gif" alt="photo not available" height="50%">
</p>


## Dependency
- Python 3.5
- Pytorch 1.1.0

## Network
The implementation of our network is in 'network.py'. It takes the sparse depth and the rgb image (normalized to 0~1) as inputs， outputs the predictions from the last, the second, and the first sub-network in sequence. The output from the last network ('output_d11') is used for final test.

    Inputs: input_d, input_rgb
    Outputs: output_d11， output_d12， output_d14
             # outputs from the last, the second, and the first sub-network

※NOTE: We recently improve the accuracy by adding the skip connections between the depth encoders and the depth decoders at the previous stage. This vision of network has 32 channels rather than 64 channels in our paper. The 32-channel network performs similarly on the test set with the 64-channel network in our paper, but has a much smaller number of parameters and a shorter run time. You can find more details in [Results](#results)

## Training
We adopt a multi-stage scheme during the training process. You can implement the training processing as

```python
loss14 = L2Loss(output_d14, label)
loss12 = L2Loss(output_d12, label)
loss11 = L2Loss(output_d11, label)

if epoch < 6:
   loss = loss14 + loss12 + loss11
elif epoch < 11:
   loss = 0.1 * loss14 + 0.1 * loss12 + loss11
else:
   loss = loss11

```
More training configurations are given in 'params.json'.

## Results
