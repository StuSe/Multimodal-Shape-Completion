# Multimodal-Shape-Completion (Testing Corner)

This repository provides PyTorch implementation of our paper:

[Multimodal Shape Completion via Conditional Generative Adversarial Networks](https://chriswu1997.github.io/files/multimodal-pc/index.html)

[Rundi Wu](https://chriswu1997.github.io)\*, [Xuelin Chen](https://xuelin-chen.github.io)\*, [Yixin Zhuang](http://www.yixin.io/), [Baoquan Chen](http://cfcs.pku.edu.cn/baoquan/) (\* equal contribution)

ECCV 2020 (spotlight)

![Overview](https://github.com/ChrisWu1997/Multimodal-Shape-Completion/blob/master/tease.png)


## Prerequisites

- Linux
- NVIDIA GPU + CUDA CuDNN
- Python 3.6



## Dependencies

1. Install python package dependencies through pip:

   ```bash
   pip install -r requirements.txt
   ```

2. Install external dependency [PyTorchEMD](https://github.com/daerduoCarey/PyTorchEMD) for the usage of EMD loss.



## Data

We use three datasets in our paper.

1. 3D-EPN

   Please download the partial scan point cloud data from [their website](http://kaldir.vc.in.tum.de/adai/CNNComplete/shapenet_dim32_sdf_pc.zip) and extract it into `data` folder. For the complete point clouds data, please download it from [PKU disk](https://disk.pku.edu.cn:443/link/9A3E1AC9FBA4DEBD705F028650CBE8C7) and extract it into `data` folder. Or you can follow this [blender-render script](https://github.com/xuelin-chen/blender_renderer) to virtually scan ShapeNet objects by yourself.

2. PartNet

   Please first download the [PartNet](https://www.shapenet.org/download/parts) dataset and then run our script to merge the point cloud segmentation label to the first level:

   ```bash
   cd data/partnet_process
   python pc_merge.py --src {your-path-to-PartNet-dataset}
   ```

   Also, remember to change the data_root path in the corresponding scripts for training/testing on PartNet dataset.

3. PartNet-Scan

   Please first download the [PartNet](https://www.shapenet.org/download/parts) dataset and then run our script to (1) create 4 parital mesh for each complete shape and (2) scan complete shape and parital mesh using blender.
   
   ```bash
   cd data/partnet_process
   sh gen_partnet_scan.sh {your-path-to-PartNet-dataset} {your-path-to-save-scanned-results}
   ```
   
   Note that the scan process (blender render) may take a very long time and the outputs require quite a lot disk space. You can also increase the number of sub-process (default 10) to speed up.



## Training

Training scripts can be found in `scripts` folder and please see `common.py` for specific definition of all command line parameters. For example, to train on 3DEPN chair category:

```bash
# 1. pre-train the PointNet AE
sh scripts/3depn/chair/train-3depn-chair-ae.sh

# 2. pre-train the PointNet VAE
sh scripts/3depn/chair/train-3depn-chair-vae.sh

# 3. train the conditional GAN for multimodal shape completion
sh scripts/3depn/chair/train-3depn-chair-gan.sh

```

Training log and model weights will be saved in `proj_log` folder by default. 



## Testing

Testing scripts can also be found in `scripts` folder. For example, to test the model trained on 3DEPN chair category:

```bash
# by default, run over all test examples and output 10 completion results for each
sh scripts/3depn/chair/test-3depn-chair-gan.sh
```

The completion results, along with the input parital shape, will be saved in `proj_log/mpc-3depn-chair/gan/results` by default. 



## Evaluation

Evaluation code can be found in `evaluation` folder. To evaluate the completion *diversity* , *fidelity* and *quality*:

```bash
cd evaluation/
# calculate Total Mutual Difference (TMD)
python total_mutual_diff.py --src {path-to-saved-testing-results}
# calculate Unidirectional Hausdorff Distance (UHD) and completeness
python completeness.py --src {path-to-saved-testing-results}
# calculate Minimal Matching Distance (MMD), this requries a tensorflow environment
python mmd.py --src {path-to-saved-testing-results} --dataset {which-dataset} --class_name {which-category} -g 0
```

Note that MMD calculation requires an compiled `external` library from [its original repo](https://github.com/optas/latent_3d_points).



## Pre-trained models

Pretrained model can be downloaded from [PKU disk](https://disk.pku.edu.cn:443/link/398E3D0C5ED9BF2BD55F9A21283815E0) or [Google Drive](https://drive.google.com/drive/folders/1OMifnRM8Ngf3mwNd7lkngQHdS64VPA-c?usp=sharing). Put the downloaded files into `proj_log` folder and then testing scripts shall be run directly. 



## Cite

Please cite our work if you find it useful:

```
@InProceedings{wu_2020_ECCV,
author = {Wu, Rundi and Chen, Xuelin and Zhuang, Yixin and Chen, Baoquan},
title = {Multimodal Shape Completion via Conditional Generative Adversarial Networks},
booktitle = {The European Conference on Computer Vision (ECCV)},
month = {August},
year = {2020}
}
```

