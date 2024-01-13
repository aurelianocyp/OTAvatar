# OTAvatar : One-shot Talking Face Avatar with Controllable Tri-plane Rendering
## [Paper](https://arxiv.org/abs/2303.14662)  |  [Demo](https://youtu.be/qpIoMYFr7Aw)

## Update

April.30: The model weight is released. The dataset is also available in Google Drive, see below for detail.

April.4: The preprocessed dataset is released, please see the `Data preparation` section. Some missing files are also uploaded.

## Get started
### Environment Setup
```
git clone https://github.com/aurelianocyp/OTAvatar
cd OTAvatar
conda env create -f environment.yaml
conda activate otavatar
```

### Pre-trained Models
Download and copy EG3D FFHQ model `ffhqrebalanced512-64.pth` [[Baidu Netdisk](https://pan.baidu.com/s/1_iEqB7qbJBK7DsjlxxW8MA?pwd=CBSR)][[Google Drive](https://drive.google.com/file/d/18RWTfeydaG_qm__rgYKPdxKcS0D_f2mr/view?usp=share_link)] to the `pretrained` directory. It is the `ffhqrebalanced512-64.pkl` file obtained from [webpage](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/research/models/eg3d), and converted to `.pth` format using the [pkl2pth](https://github.com/oneThousand1000/EG3D-projector/blob/master/eg3d/convert_pkl_2_pth.py) script.

Download [`arcface_resnet18.pth`](https://github.com/ronghuaiyang/arcface-pytorch) and save to the `pretrained` directory.

### Data preparation 
We upload the processed dataset `hdtf_lmdb_inv` in [[Baidu Netdisk](https://pan.baidu.com/s/1yXvLR2WvwdamGrWFnNefZw?pwd=CBSR)][[Google Drive](https://drive.google.com/drive/folders/1kDIhBU_Cz_HyfYHAuETcDquCuZBDfqeu?usp=share_link)]. In the root directory,

```
mkdir datasets
mv <your hdtf_lmdb_inv path> datasets/
```

Generally the processing scripts is a mixture of that in [PIRenderer](https://github.com/RenYurui/PIRender) and [ADNeRF](https://github.com/YudongGuo/AD-NeRF). We plan to further open a new repo to upload our revised preocessing script.

### Face Animation
Create the folder `result/otavatar`if it does not exist. Place the model downloaded from [[Baidu Netdisk](https://pan.baidu.com/s/1bLlXMUT4r76MQc4vywLreg?pwd=CBSR)][[Google Drive](https://drive.google.com/file/d/1JW1ieAgeu5qugC41O7zjn7IA49Jfnytt/view?usp=share_link)] under this directory. Run,
```
export CUDA_VISIBLE_DEVICES=0
python -m torch.distributed.launch --nproc_per_node=1 --master_port 12345 inference_refine_1D_cam.py \
--config ./config/otavatar.yaml \
--name otavatar \
--no_resume \
--which_iter 2000 \
--image_size 512 \
--ws_plus \
--cross_id \
--cross_id_target WRA_EricCantor_000 \
--output_dir ./result/otavatar/evaluation/cross_ws_plus_WRA_EricCantor_000
```
To animate each identity given the motion from `WRA_EricCantor_000`.

Or simply run,
```
sh scripts/inference.sh
```
run的时候发现必须要有数据集才行，数据集好大，放弃了
### Start Training
Run,
```
export CUDA_VISIBLE_DEVICES=0,1,2,3
python -m torch.distributed.launch --nproc_per_node=4 --master_port 12346 train_inversion.py \
--config ./config/otavatar.yaml \
--name otavatar
```

Or simply run,
```
sh scripts/train.sh
```

### Acknowledgement
We appreciate the model or code from [EG3D](https://github.com/NVlabs/eg3d), [PIRenderer](https://github.com/RenYurui/PIRender), [StyleHEAT](https://github.com/FeiiYin/StyleHEAT), [EG3D-projector](https://github.com/oneThousand1000/EG3D-projector).

### Citation
If you find this work helpful, please cite:
```
@article{ma2023otavatar,
  title={OTAvatar: One-shot Talking Face Avatar with Controllable Tri-plane Rendering},
  author={Ma, Zhiyuan and Zhu, Xiangyu and Qi, Guojun and Lei, Zhen and Zhang, Lei},
  journal={arXiv preprint arXiv:2303.14662},
  year={2023}
}
```

