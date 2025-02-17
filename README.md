# Perception-Aware Multi-Sensor Fusion for 3D LiDAR Semantic Segmentation (ICCV 2021)

## Introduction

In this work, we aim to explore an effective multi-sensor (LiDAR and RGB camera) fusion method for 3D LiDAR semantic segmentation. Existing fusion-based methods mainly conduct feature fusion in the LiDAR coordinates, which leads to the loss of perceptual features (shape or textures) of RGB images.    In contrast, we try to fuse the information from the two modalities in the camera coordinates, and propose a Perception-aware Multi-sensor Fusion (PMF) scheme. More details can be found in our paper.

![image-20211013141408045](assets/image-20211013141408045.png)

## Experimental Results

[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/perception-aware-multi-sensor-fusion-for-3d/lidar-semantic-segmentation-on-nuscenes)](https://paperswithcode.com/sota/lidar-semantic-segmentation-on-nuscenes?p=perception-aware-multi-sensor-fusion-for-3d)

[Leader board of SensatUrban@ICCV2021](https://competitions.codalab.org/competitions/31519#results)

![image-20211013144333265](assets/image-20211013144333265.png)

## More Results

More details can be found in [file](./more_experiment_config.md).

| Method          | Dataset                                                      | mIoU (%)      |
| --------------- | ------------------------------------------------------------ | ------------- |
| PMF-ResNet34    | SemanticKITTI Validation Set                                 | 63.9          |
| PMF-ResNet34    | nuScenes Validation Set                                      | 76.9          |
| PMF-ResNet50    | nuScenes Validation Set                                      | 79.4          |
| PMF48-ResNet101 | SensatUrban Test Set ([ICCV2021 Competition](https://competitions.codalab.org/competitions/31519#results)) | 66.2 (Rank 5) |



## How To Use?

### Requirements

First you need to create conda environment. This repository 

*    python 3.7
*    pytorch 1.8.1 + cuda 10.2

You can easily create and activate env with

```bash
conda env create -f environment.yml
conda activate PMF
```

### Folder structure

```bash
|--- pc_processor/ # python package for point cloud processing
	|--- checkpoint/ # generate log for experiments
	|--- dataset/ # function for dataset pre-processing
	|--- layers/
	|--- loss/ 
	|--- metrices/ 
	|--- models/
	|--- postproc/ 
	|--- utils/
|--- tasks/ 
	|--- pmf/ # training code of PMF
	|--- pmf_eval_nuscenes/ # evaluation code of PMF on nuScenes
		|--- testset_eval/ # merge the results of salsanext and PMF to generate results for testset
		|--- xxx.py # evaluation code of PMF on nuScenes
	|--- pmf_eval_semantickitti/ # evaluation code of PMF on SemanticKITTI
	|--- salsanext/ # training code of SalsaNext (modified from the official version)
	|--- salsanext_eval_nuscenes/ # evaluation code of SalsaNext on nuScenes
```

### Dataset structure

```bash
|--- semantickitti/dataset/
	|--- sequences/
		|--- 00/ # track number
			|--- poses.txt # poses for annotation
			|--- calib.txt # camera calibration
			|--- image_2/ # left images
				|--- 000000.png
				|--- 000001.png
			|--- image_3/ # right images
			|--- labels/ #labels for lidar scans
				|--- 000000.label
				|--- 000001.label
			|--- velodyne/ # lidar scans
				|--- 000000.bin
				|--- 000001.bin
		|--- 01/
		.
		.
		. 
		|--- 21/
```

### Training

#### Code for training

```bash
|--- pmf/
	|--- config_server_kitti.yaml # configuration file for SemanticKITTI
	|--- config_server_nus.yaml # configuration file for nuScenes
	|--- main.py # main function
	|--- trainer.py # training code
	|--- option.py # parser for configuration file
	|--- run.sh # executable script 
```

#### Steps

1. Enter `tasks/pmf`and modify the configuration file `config_server_kitti.yaml`. You need to set `data_root` to the path of your dataset。Additionally, you can set`gpu`, `batch_size` as you need.
2. Modify  `run.sh` and make sure the value of `nproc_per_node` is equal to the number of GPU in the configuration file.
3. Run the following command

```bash
./run.sh
# or bash run.sh
```

4. If you run the script successfully , you can find the log folder at `PMF/experiments/PMF-SemanticKitti`. The structure of log folder:

```
|--- log_dataset_network_xxxx/
	|--- checkpoint/ 
	|--- code/ 
	|--- log/ 
	|--- events.out.tfevents.xxx
```

The output of the console:

![image-20211013152939956](assets/image-20211013152939956.png)

### Evaluation

#### Code for evaluation

```
|--- pmf_eval_semantickitti/ 
	|--- config_server_kitti.yaml 
	|--- infer.py 
	|--- option.py
```

#### Steps

1. Enter `tasks/pmf_eval_semantickitti` and modify the configuration file  `config_server_kitti.yaml`. You need to set  `data_root`  to the path of your dataset and set`pretrained_path` to the log folder of the trained model.
2. Run the following command:

```bash
python infer.py config_server_kitti.yaml
```

3. If you run the script successfully, you will find the log folder of evaluation in the folder of your trained model. 

```
|--- PMF/experiments/PMF-SemanticKitti/log_xxxx/ 
	|--- Eval_xxxxx/ 
		|--- code/ 
		|--- log/ 
		|--- pred/ 
```

## Citation

```
@InProceedings{Zhuang_2021_ICCV,
    author    = {Zhuang, Zhuangwei and Li, Rong and Jia, Kui and Wang, Qicheng and Li, Yuanqing and Tan, Mingkui},
    title     = {Perception-Aware Multi-Sensor Fusion for 3D LiDAR Semantic Segmentation},
    booktitle = {Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)},
    month     = {October},
    year      = {2021},
    pages     = {16280-16290}
}
```





