[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/slowfast-network-for-continuous-sign-language/sign-language-recognition-on-csl-daily)](https://paperswithcode.com/sota/sign-language-recognition-on-csl-daily?p=slowfast-network-for-continuous-sign-language)

[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/slowfast-network-for-continuous-sign-language/sign-language-recognition-on-rwth-phoenix)](https://paperswithcode.com/sota/sign-language-recognition-on-rwth-phoenix?p=slowfast-network-for-continuous-sign-language)

[![PWC](https://img.shields.io/endpoint.svg?url=https://paperswithcode.com/badge/slowfast-network-for-continuous-sign-language/sign-language-recognition-on-rwth-phoenix-1)](https://paperswithcode.com/sota/sign-language-recognition-on-rwth-phoenix-1?p=slowfast-network-for-continuous-sign-language)

# SlowFast Network for Continuous Sign Language Recognition

This repository is a implementation of our paper: "SlowFast Network for Continuous Sign Language Recognition"

> [SlowFast Network for Continuous Sign Language Recognition](https://ieeexplore.ieee.org/abstract/document/10445841/)
>
> Junseok Ahn, [Youngjoon Jang](https://art-jang.github.io/), [Joon Son Chung](https://mm.kaist.ac.kr/joon/)
>
> Accepted to ICASSP 2024

This repo is based on [VAC (ICCV 2021)](https://github.com/ycmin95/VAC_CSLR), [CorrNet (CVPR 2023)](https://github.com/hulianyuyy/CorrNet), and [PySlowFast](https://github.com/facebookresearch/SlowFast).

## Abstract
<p align='center'>
  <img width='90%' src='./framework.png'/>
</p>
  <b>
Fig. 1. Overall Architecture. Our framework extracts spatial and dynamic features in parallel using the Slow and Fast pathways. Bi-directional Feature Fusion (BFF) facilitates the exchange of rich information between two pathways. Pathway Feature Enhancement (PFE) amplifies the sequence feature of each pathway by utilising characteristics sourced from different sample rates. 
</b>
<br /> <br />
The objective of this work is the effective extraction of spatial and dynamic features for Continuous Sign Language Recognition (CSLR). To accomplish this, we utilise a two-pathway SlowFast network, where each pathway operates at distinct temporal resolutions to separately capture spatial (hand shapes, facial expressions) and dynamic (movements) information. In addition, we introduce two distinct feature fusion methods, carefully designed for the characteristics of CSLR: (1) Bi-directional Feature Fusion (BFF), which facilitates the transfer of dynamic semantics into spatial semantics and vice versa; and (2) Pathway Feature Enhancement (PFE), which enriches dynamic and spatial representations through auxiliary subnetworks, while avoiding the need for extra inference time. As a result, our model further strengthens spatial and dynamic representations in parallel. We demonstrate that the proposed framework outperforms the current state-of-the-art performance on popular CSLR datasets, including PHOENIX14, PHOENIX14-T, and CSL-Daily.

## Prerequisites
- Python == 3.8
- PyTorch == 1.13
- ctcdecode == 1.0.3 [[parlance/ctcdecode]](https://github.com/parlance/ctcdecode)，for beam search decode.
- You can install other required modules by running  
  `pip install -r requirements.txt`

I have used the Colab

## Data Preparation
Please follow the instruction in [CorrNet](https://github.com/hulianyuyy/CorrNet) github repo to download and preprocess the datasets (PHOENIX2014, PHOENIX2014-T, CSL-Daily).
The structure of dataset directory is as follows (There may be other additional directories.):
```
dataset
├── phoenix2014
│   └── phoenix-2014-multisigner
│       └── features
│           └── fullFrame-256x256px
├── phoenix2014-T
│   └── features
│       └── fullFrame-256x256px
└── CSL-Daily
    └── sentence
        └── frames_256x256
```
## PHOENIX2014 dataset
Download the RWTH-PHOENIX-Weather 2014 Dataset . Our experiments based on phoenix-2014.v3.tar.gz.

After finishing dataset download, extract it. It is suggested to make a soft link toward downloaded dataset.
ln -s PATH_TO_DATASET/phoenix2014-release ./dataset/phoenix2014

The original image sequence is 210x260, we resize it to 256x256 for augmentation. Run the following command to generate gloss dict and resize image sequence.

cd ./preprocess
python dataset_preprocess.py --process-image --multiprocessing

## Inference
Dataset can be downloaded from  [[Dataset]](https://www-i6.informatik.rwth-aachen.de/~koller/RWTH-PHOENIX/).
| Dataset       | Dev WER (%) | Test WER (%) |
|:--------------| :-----: | :------: |
| PHOENIX2014   | 18.01 | 18.28 |
| PHOENIX2014-T | 17.66 | 18.71 |
| CSL-Daily     | 25.49 | 24.85 |

In the original paper, SlowFastSign reported a WER of 18.01% on the dev set and 18.28% on the test set for the PHOENIX14 dataset.
After running the released code, we obtained results of 17.91% on the dev set and 18.16% on the test set, consistent with the original results, though slightly higher due to differences in environment, hardware setup, or hyper-parameters.


In this, I have performed the PHOENIX2014 which is a huge dataset consisting of 57.3 GB

To evaluate the pre-trained model, run the command below:
```bash
python main.py --device 0 --dataset phoenix2014 --phase test --load-weights ./best_checkpoints/phoenix2014_dev_18.01_test_18.28.pt --work-dir ./work_dir/phoenix2014_test/
```
## Checkpoints
Here is the Checkpoints link [[Google Drive]](https://drive.google.com/drive/folders/1kAZprB3_DrCQwIGyfM0AbnSpL8u8Van7).

## Structure of the dataset

It consists of 
RWTH-PHOENIX Weather 2014 Contents:
phoenix-2014.v2.tar.gz
|__README
   multisigner
   |
   |__signerindependent-SI5
      |
      |__README
         annotations
         evaluation
         |
         |__features
            |__ fullFrame-210x260px
## Training 
Before you start training, download the pre-trained SlowFast pkl file by running the following code:
```bash
mkdir ckpt && cd ckpt
wget https://dl.fbaipublicfiles.com/pyslowfast/model_zoo/ava/pretrain/SLOWFAST_64x2_R101_50_50.pkl
```

To train the SlowFastSign model, run the command below:
```bash
python main.py --device 0 --dataset phoenix2014 --loss-weights Slow=0.25 Fast=0.25 --work-dir ./work_dir/phoenix2014/
```
You can check other arguments for training from `utils/parameters.py`

## Citation
If you find our work useful for your research, please cite our work with the following bibtex:

```bibtex
@inproceedings{ahn2024slowfast,
  author={Ahn, Junseok and Jang, Youngjoon and Chung, Joon Son},
  booktitle={IEEE International Conference on Acoustics, Speech and Signal Processing}, 
  title={Slowfast Network for Continuous Sign Language Recognition}, 
  year={2024},
  pages={3920-3924},
  doi={10.1109/ICASSP48485.2024.10445841}
}
```

