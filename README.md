# MonoDepth
![demo.gif animation](readme_images/demo.gif)

This repo is inspired by an amazing work of [Clément Godard](http://www0.cs.ucl.ac.uk/staff/C.Godard/), [Oisin Mac Aodha](http://vision.caltech.edu/~macaodha/) and [Gabriel J. Brostow](http://www0.cs.ucl.ac.uk/staff/g.brostow/) for Unsupervised Monocular Depth Estimation.
Original code and paper could be found via following links:
1. [Original repo](https://github.com/mrharicot/monodepth)
2. [Original paper](https://arxiv.org/abs/1609.03677)

## MonoDepth-PyTorch
This repository contains code and additional parts for the PyTorch port of the MonoDepth Deep Learning algorithm. For more information about original work please visit [author's website](http://visual.cs.ucl.ac.uk/pubs/monoDepth/)

## Purpose

Purpose of this repository is to make more lightweighted model for depth estimation with better accuracy.
In our version of MonoDepth we used Resnet50 as encoder. It was slightly changed (with one more lateral shrinkage) as well as in the original repo.
Also we add Resnet18 version and used batch normalisation in both cases for training stability.
Moreover, we made flexible feature extractor with any version of original Resnet from torchvision models zoo
 with option to use pretrained models.

## Train results

Results presented on the gif may be obtained using the model pretrained for **150** epochs on the whole dataset with initial **lr = 0.01** and **batch_size = 20** with **resnet18** as encoder.



## Dataset
### KITTI

This algorithm requires stereo-pair images for training and single images for testing.
[KITTI](http://www.cvlibs.net/datasets/kitti/raw_data.php) dataset was used for training.
It contains 38237 training samples.
Raw dataset (about 175 GB) can be downloaded by running:
```shell
wget -i kitti_archives_to_download.txt -P ~/my/output/folder/
```
kitti_archives_to_download.txt may be found in this repo.

## Dataloader
Dataloader assumes the following structure of the folder with train examples (**'data_dir'** argument contains path to that folder):
The folder contains subfolders with following folders "image_02/data" for left images and  "image_03/data" for right images.
Such structure is default for KITTI dataset

Example data folder structure (path to the "kitti" directory should be passed as **'data_dir'** in this example):
```
data
├── kitti
│   ├── 2011_09_26_drive_0001_sync
│   │   ├── image_02
│   │   │   ├─ data
│   │   │   │   ├── 0000000000.png
│   │   │   │   └── ...
│   │   ├── image_03
│   │   │   ├── data
│   │   │   │   ├── 0000000000.png
│   │   │   │   └── ...
│   ├── ...
├── models
├── output
├── test
│   ├── left
│   │   ├── test_1.jpg
│   │   └── ...
```
    
## Training
Example of training can be find in [Monodepth](Monodepth.ipynb) notebook.

Model class from main_monodepth_pytorch.py should be initialized with following params (as easydict) for training:
 - `data_dir`: path to the dataset folder
 - `val_data_dir`: path to the validation dataset folder
 - `model_path`: path to save the trained model
 - `output_directory`: where save dispairities for tested images
 - `input_height`
 - `input_width`
 - `model`: model for encoder (resnet18_md or resnet50_md or any torchvision version of Resnet (resnet18, resnet34 etc.)
 - `pretrained`: if use a torchvision model it's possible to download weights for pretrained model
 - `mode`: train or test
 - `epochs`: number of epochs,
 - `learning_rate`
 - `batch_size`
 - `adjust_lr`: apply learning rate decay or not
 - `tensor_type`:'torch.cuda.FloatTensor' or 'torch.FloatTensor'
 - `do_augmentation`:do data augmentation or not
 - `augment_parameters`:lowest and highest values for gamma, lightness and color respectively
 - `print_images`
 - `print_weights`
 - `input_channels` Number of channels in input tensor (3 for RGB images)
 - `num_workers` Number of workers to use in dataloader

Optionally after initialization we can load pretrained model via load model.

After that calling train() on Model class object starts training process.

Also it can be started via calling main_monodepth_pytorch.py through the terminal and feeding parameters as argparse arguments.

## Pretrained model

One of our pretrained models which showed best results may be downloaded from [here](https://my.pcloud.com/publink/show?code=XZdFzu7ZfCAEf0uj8zRhDrBsjuEoeSo2QXak).
For training following parameters were used:
`model`:'resnet18_md'
`epochs`:150,
`learning_rate`:1e-2,
`batch_size`:20,
`adjust_lr`:True 
    
## Testing
Example of testing can be find in [Monodepth](Monodepth.ipynb) notebook.

Model class from main_monodepth_pytorch.py should be initialized with following params (as easydict) for testing:
 - `data_dir`: path to the dataset folder
 - `model_path`: path to save the trained model
 - `pretrained`: 
 - `output_directory`: where save dispairities for tested images
 - `input_height`
 - `input_width`
 - `model`: model for encoder (resnet18 or resnet50)
 - `mode`: train or test
 - `input_channels` Number of channels in input tensor (3 for RGB images)
 - `num_workers` Number of workers to use in dataloader
 
After that calling test() on Model class object starts testing process.

Also it can be started via calling [main_monodepth_pytorch.py](main_monodepth_pytorch.py) through the terminal and feeding parameters asargparse arguments. 
    
## Requirements
This code was tested with PyTorch 0.4.1, CUDA 9.1 and Ubuntu 16.04.
