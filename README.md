<p align="center">
    <!-- community badges -->
    <a href="https://discord.gg/uMbNqcraFc"><img src="https://img.shields.io/badge/Join-Discord-blue.svg"/></a>
    <!-- doc badges -->
    <a href='https://plenoptix-nerfstudio.readthedocs-hosted.com/en/latest/?badge=latest'>
        <img src='https://readthedocs.com/projects/plenoptix-nerfstudio/badge/?version=latest' alt='Documentation Status' />
    </a>
    <!-- pi package badge -->
    <a href="https://badge.fury.io/py/nerfstudio"><img src="https://badge.fury.io/py/nerfstudio.svg" alt="PyPI version"></a>
    <!-- code check badges -->
    <a href='https://github.com/nerfstudio-project/nerfstudio/actions/workflows/core_code_checks.yml'>
        <img src='https://github.com/nerfstudio-project/nerfstudio/actions/workflows/core_code_checks.yml/badge.svg' alt='Test Status' />
    </a>
    <a href='https://github.com/nerfstudio-project/nerfstudio/actions/workflows/viewer_build_deploy.yml'>
        <img src='https://github.com/nerfstudio-project/nerfstudio/actions/workflows/viewer_build_deploy.yml/badge.svg' alt='Viewer build Status' />
    </a>
    <!-- license badge -->
    <a href="https://github.com/nerfstudio-project/nerfstudio/blob/master/LICENSE">
        <img alt="License" src="https://img.shields.io/badge/License-Apache_2.0-blue.svg">
    </a>
</p>


<p align="center">
    <!-- pypi-strip -->
    <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://docs.nerf.studio/en/latest/_images/logo-dark.png">
    <source media="(prefers-color-scheme: light)" srcset="https://docs.nerf.studio/en/latest/_images/logo.png">
    <!-- /pypi-strip -->
    <img alt="nerfstudio" src="https://docs.nerf.studio/en/latest/_images/logo.png" width="400">
    <!-- pypi-strip -->
    </picture>
    <!-- /pypi-strip -->
</p>
<!-- Use this for pypi package (and disable above). Hacky workaround -->
<!-- <p align="center">
    <img alt="nerfstudio" src="https://docs.nerf.studio/en/latest/_images/logo.png" width="400">
</p> -->


<p align="center"> A collaboration friendly studio for NeRFs </p>

<p align="center">
    <a href="https://docs.nerf.studio">
        <img alt="documentation" src="https://user-images.githubusercontent.com/3310961/194022638-b591ce16-76e3-4ba6-9d70-3be252b36084.png" width="150">
    </a>
    <a href="https://viewer.nerf.studio/">
        <img alt="viewer" src="https://user-images.githubusercontent.com/3310961/194022636-a9efb85a-14fd-4002-8ed4-4ca434898b5a.png" width="150">
    </a>
    <a href="https://colab.research.google.com/github/nerfstudio-project/nerfstudio/blob/main/colab/demo.ipynb">
        <img alt="colab" src="https://raw.githubusercontent.com/nerfstudio-project/nerfstudio/main/docs/_static/imgs/readme_colab.png" width="150">
    </a>
</p>



# Quickstart

该项目主要目的是为了制作一个后端应用，布置在服务器上，实现通过网络请求，调用三维重建算法，存储重建成果，对重建成果进行增删改查。

该三维重建算法主要是通过一系列图片，合成新视角图片和mesh文件。

该项目主要是两个进程，进程1是flask app，监听请求，将训练任务放入redis队列；进程2是worker进程，用于监听队列，当队列有任务，则worker接受并进行处理。

## 1. 安装依赖 ： 设置运行虚拟环境

### 前置程序

主机必须用NVIDIA显卡并安装CUDA。最好是11.8CUDA。在这里看CUDA详细信息。[here](https://docs.nvidia.com/cuda/cuda-quick-start-guide/index.html)

### 创建环境

Nerfstudio 需要 `python >= 3.8`. 

nerfstudio用Conda来管理包，提前下载好 [Conda](https://docs.conda.io/en/latest/miniconda.html) 

```bash
conda create --name nerfstudio -y python=3.8
conda activate nerfstudio
pip install --upgrade pip
```

### 下载依赖包

安装CUDA版Pytorch和  [tiny-cuda-nn](https://github.com/NVlabs/tiny-cuda-nn)。 安装`tiny-cuda-nn`需要安装cuda-toolkit

CUDA 11.7:

```bash
pip install torch==2.0.1+cu117 torchvision==0.15.2+cu117 --extra-index-url https://download.pytorch.org/whl/cu117

conda install -c https://conda.anaconda.org/nvidia/label/cuda-11.7.1 cuda-toolkit

pip install ninja git+https://github.com/NVlabs/tiny-cuda-nn/#subdirectory=bindings/torch
```

CUDA 11.8:

```bash
pip install torch==2.0.1+cu118 torchvision==0.15.2+cu118 --extra-index-url https://download.pytorch.org/whl/cu118

conda install -c https://conda.anaconda.org/nvidia/label/cuda-11.8.0 cuda-toolkit  # 很大

pip install ninja

pip install ninja git+https://github.com/NVlabs/tiny-cuda-nn/#subdirectory=bindings/torch
```

二选一



FFMPEG（必须）

```shell
sudo apt upgrade 
sudo apt install ffmpeg
```



Colmap（必须）

`对应主机版本下载linux版、windows版`

[Installation — COLMAP 3.8-dev documentation](https://colmap.github.io/install.html) 



```shell
#linux版本

sudo apt-get install \
    git \
    cmake \
    ninja-build \
    build-essential \
    libboost-program-options-dev \
    libboost-filesystem-dev \
    libboost-graph-dev \
    libboost-system-dev \
    libboost-test-dev \
    libeigen3-dev \
    libflann-dev \
    libfreeimage-dev \
    libmetis-dev \
    libgoogle-glog-dev \
    libgflags-dev \
    libsqlite3-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev \
    libceres-dev
    libgtest-dev
```

```shell
git clone https://github.com/colmap/colmap.git
cd colmap
git checkout dev
mkdir build
cd build
cmake .. -GNinja -DCMAKE_CUDA_ARCHITECTURES= 'all-major'
ninja
sudo ninja install
```





### 下载 nerfstudio_restful项目

nerfstudio项目详细的依赖安装文档 [Dependencies](https://github.com/nerfstudio-project/nerfstudio/blob/main/docs/quickstart/installation.md#dependencies)

安装好依赖后，克隆项目

```bash
git clone https://github.com/FuturaTino/nerfstudio_restful.git
cd nerfstudio_restful
pip install --upgrade pip setuptools
pip install -e .
```

 

## 其他选择：云服务器

直接选择镜像文件，创建服务器实例，启动服务即可。

### 其他选择：Docker部署

后续写







## 2.启动服务： Flask app 和 rq worker

```shell
conda activate nerfstudio
cd nerfstudio_restful
python ns_api/utils_worker.py -q nerf_queue
# 打开一个新的terminal
python ns_api/app.py 
```



## 3. 通过接口访问三维重建服务

假设请求网址为 http://127.0.0.1:8080，

后端语言与框架：python flask

客户端语言： python

### 创建任务

1. 首先向http://127.0.0.1:8080/capture发送POST请求，携带数据`username`与`title`，服务器会初始化一个 **Capture对象**，用以这次三维重建任务的所有信息。POST请求会返回一个Response，其中包含Capture对象的详细信息，即一个 **json文件**，其中包含这次三维重建所需要的所有信息。我们获取Response中的`source_url`和`slug`字段。 Response内容格式[点这里](###Response)。 

```python
import requests

username = "Zhangsan"
capture_title = "table"

response = requests.post("https://createmesh.com/capture",
                         data={'title': capture_title,
                               'username': username})
capture_data = response.json()
upload_url = capture_data['source_url']
slug = capture_data['slug']

print(capture_data)
print("Srouce URL for uploading:", source_url)
print("Capture slug:", slug)
```



### 获取任务信息



### 上传视频

2. 获取Response中的`source_url`字段`https://bucket-storage.com/zhangsan-table-122435`，**向该网址发送PUT请求，上传视频文件**。该请求没有Response，只是单向的文件传输。获得上传成功的code即可。

```python
with open("video.mp4", "rb") as f:
    payload = f.read()

# source_url from step (1)
response = requests.put(source_url,data=payload)

print(response.text)



# curl 
curl -X PUT -T "C:\Users\future\Desktop\eraLi.mp4" "http://f-test-bucket.oss-cn-hangzhou.aliyuncs.com/future-eraLi-102047.mp4?OSSAccessKeyId=LTAI5t6SxcPWVVEm5J48uHZg&Expires=1690857047&Signature=Cfl%2Fmv4Mro%2FirteTIWh%2Be8BKxRM%3D"
```





### **启动训练**

2. 获取 `slug`字段，向 http://127.0.0.1:8080/capture<slug> 发送 POST请求。

```python
# slug from Capture step
response = requests.post(f"http://127.0.0.1:8080/capture/{slug}")

print(response.text) # f"{slug} has enequeued"
```



### 下载模型

4. 通过`slug`获取任务的实时信息。 向 https://createmesh.com/capture/<slug>， 发送GET请求。当任务完成，status字段为变为`Finished`。获取`result_url`的字段，向`result_url`发送GET请求，即可下载mesh文件压缩包。

```python
# slug from Capture step
response = requests.get(f"http://127.0.0.1:8080/capture/{slug}")

print(response.text)
```



### Response

Response中的数据结构

```python
{
    "slug" : "zhangsan-table-122435",
    "title": "table",
    "type" : "reconstruction",
    "date" : "2023.7.28-23:41:43",
    "username": "zhangsan",
    "status": "wait_for_upload",
    "latest_run_status": "wait_for_upload",
    "latest_run_progress": 0,
    "latest_run_current_stage": "wait_for_upload",
    "source_url": "https://bucket-storage.com/zhangsan-table-122435",
    "result_url": ""
}
```

解释如下：

- slug: 任务的唯一标识,表明是用户zhangsan创建的,关于table,创建时间是23:41:43
- title: 任务的标题或名称,这里是"table"
- type: 任务类型,这里是"reconstruction",表示3D重建任务（目前就这一个值）
- date: 任务的创建时间
- username: 创建任务的用户名称
- status: 任务整体状态,这里是"wait_for_upload",表示正在等待用户上传文件
- latest_run_status: 最近一次运行的状态,同样为"wait_for_upload"
- latest_run_progress: 最近一次运行的进度,这里是0,表示刚创建,还没开始处理
- latest_run_current_stage: 最近一次运行的当前执行阶段,同上 为"wait_for_upload"
- source_url: 源文件上传的地址
- result_url: 生成结果文件的地址,现在还为空





# 算法原理



## **前提**

1. 相机位姿信息已知（use sfm algorithm to extract extincs and intincs）。
2. 场景是静态的。
3. 照片之间具有稠密特征点（每个特征点都能在多个照片里出现）。



## 流程

![../../_images/models_nerf-pipeline-light.png](https://github.com/FuturaTino/TyporaImages/raw/main//TyporaImages/models_nerf-pipeline-light.png)

神经辐射场（Neural Radiance Field）算法是一种用神经网络编码的体积化表达（volumetric representation）来存储一个三维场景的方法。它和网格（Mesh） 、点云（Point Cloud） 都不一样。

对神经辐射场中的每个点，都储存着体密度（Density）和颜色信息（RGB Color）。每个点的 体密度和颜色 都需要把该点的位置坐标与方向 经过神经网络前向传播来获得。体密度记录了该点存在的微分似然值（differential likelihood），通俗理解就是该点存在实体的可能性。



## 体积渲染

![2](https://github.com/FuturaTino/TyporaImages/raw/main//TyporaImages/2.png)



现在用神经网络表达了整个三维场景，我们需要一个方式去渲染新视角的照片。为了实现这个目的，原文使是模拟在相机位置上投射一条光线到照片的每个像素点，然后使用图形学中的体积渲染（Volume rendering） 来组合（Composite）光线上所有点的颜色，最获取预测像素点的颜色。

看原文体积渲染的公式，这种组合方式很像PS中把多个图层同时垒到一起。

![../../_images/models_nerf-pipeline-sampler-light.png](https://github.com/FuturaTino/TyporaImages/raw/main//TyporaImages/models_nerf-pipeline-sampler-light.png)

上面讲到，要在相机到照片像素点之间的光线上组合所有点，来预测该像素点的颜色。光线是连续的，上面有无限多个点，无法穷尽，所以要进行采样。最简单是进行等距采样（Uniform Sample），用等间隔距离在光线上进行采样，将Points 第一次输入神经网络，将输出的体密度与颜色进行体积渲染，获取一个粗糙的图片和每个点的采样权重。采样权重（Weights）由体密度计算而来，表明了光线上哪些点具有实体的可能性更大。第二次进行概率分布采样（Possibility Distribution Funtion sample），仅仅在权重值大的区域内进行采样，这些区域是最接近实体表面的。所以采样效果更好。 



# 后端框架

项目主要用到以下组件

| 组件  | 解释                                                        |
| ----- | ----------------------------------------------------------- |
| 主机  | 一个进程flask app和一个worker进程处理长时间任务。           |
| mysql | 目前使用阿里云的mysql服务器，预计到10月15号到期             |
| redis | 同使用阿里云                                                |
| oss   | 目前使用阿里云的oss服务，主要使用上传、下载、签名url三个API |





## 流程图

![ns_train22](https://github.com/FuturaTino/TyporaImages/raw/main//TyporaImages/ns_train22.png)

## Redis作用示意图

![Flask 集成 Redis Queue 的调用时序图](https://github.com/FuturaTino/TyporaImages/raw/main//images/68747470733a2f2f7465737464726976656e2e696f2f7374617469632f696d616765732f626c6f672f666c61736b2d72712f666c61736b2d72712d666c6f772e706e67)





# Built On

<a href="https://github.com/brentyi/tyro">
<!-- pypi-strip -->

<picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://brentyi.github.io/tyro/_static/logo-dark.svg" />
<!-- /pypi-strip -->
    <img alt="tyro logo" src="https://brentyi.github.io/tyro/_static/logo-light.svg" width="150px" />
<!-- pypi-strip -->
</picture>

<!-- /pypi-strip -->
</a>

- Easy-to-use config system
- Developed by [Brent Yi](https://brentyi.com/)

<a href="https://github.com/KAIR-BAIR/nerfacc">
<!-- pypi-strip -->

<picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://user-images.githubusercontent.com/3310961/199083722-881a2372-62c1-4255-8521-31a95a721851.png" />
<!-- /pypi-strip -->
    <img alt="tyro logo" src="https://user-images.githubusercontent.com/3310961/199084143-0d63eb40-3f35-48d2-a9d5-78d1d60b7d66.png" width="250px" />
<!-- pypi-strip -->
</picture>

<!-- /pypi-strip -->
</a>

- Library for accelerating NeRF renders
- Developed by [Ruilong Li](https://www.liruilong.cn/)

# Citation

You can find a paper writeup of the framework on [arXiv](https://arxiv.org/abs/2302.04264).

If you use this library or find the documentation useful for your research, please consider citing:

```
@inproceedings{nerfstudio,
	title        = {Nerfstudio: A Modular Framework for Neural Radiance Field Development},
	author       = {
		Tancik, Matthew and Weber, Ethan and Ng, Evonne and Li, Ruilong and Yi, Brent
		and Kerr, Justin and Wang, Terrance and Kristoffersen, Alexander and Austin,
		Jake and Salahi, Kamyar and Ahuja, Abhik and McAllister, David and Kanazawa,
		Angjoo
	},
	year         = 2023,
	booktitle    = {ACM SIGGRAPH 2023 Conference Proceedings},
	series       = {SIGGRAPH '23}
}
```

# Contributors

<a href="https://github.com/nerfstudio-project/nerfstudio/graphs/contributors">
  <img src="https://github.com/FuturaTino/TyporaImages/raw/main//TyporaImages/image" />
</a>
