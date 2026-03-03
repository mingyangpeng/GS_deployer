FROM pytorch/pytorch:2.4.0-cuda11.8-cudnn9-runtime
# 核心环境变量（全局生效，避免交互式配置）
ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Asia/Shanghai \
    LC_ALL=C.UTF-8 \
    LANG=C.UTF-8 \
    # CUDA 路径配置
    CUDA_HOME=/usr/local/cuda \
    PATH=/usr/local/cuda/bin:/usr/local/bin:$PATH \
    LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/lib:$LD_LIBRARY_PATH \
    # Python 路径（方便挂载代码）
    PYTHONPATH=/root/code:$PYTHONPATH

# 更换阿里云 Ubuntu 22.04 源（加速国内下载）
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    # 新增 universe 仓库（Ubuntu 22.04 部分依赖需要）
    echo "deb http://mirrors.aliyun.com/ubuntu/ jammy universe" >> /etc/apt/sources.list

# 安装系统依赖（NeRF/GS 编译+运行必备）
RUN apt-get update && apt-get install -y --no-install-recommends \
    # 基础编译工具
    git vim wget curl unzip build-essential cmake ninja-build pkg-config \
    # Python 环境（Ubuntu 22.04 默认 Python 3.10，兼容 NeRF/GS）
    python3 python3-pip python3-dev python3-venv python3-tk \
    # 图形/视频处理依赖
    libgl1-mesa-glx libglib2.0-0 libsm6 libxext6 libxrender-dev libegl1-mesa \
    # OpenCV 系统依赖
    libopencv-dev libjpeg-dev libpng-dev libtiff-dev \
    # 3D 加速依赖
    libopenmpi-dev libomp-dev \
    # 清理缓存（减少镜像体积）
    && rm -rf /var/lib/apt/lists/* \
    # 软链接 python/pip 到 python3/pip3
    && ln -sf /usr/bin/python3 /usr/bin/python \
    && ln -sf /usr/bin/pip3 /usr/bin/pip

# 升级 pip 并配置清华源（加速 Python 包下载）
RUN pip install --upgrade pip setuptools wheel && \
    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple/

# 安装 NeRF/GS 核心 Python 依赖（适配 CUDA 12.4）
# torch==2.4.0 torchvision==0.19.0 torchaudio==2.4.0 --index-url https://download.pytorch.org/whl/cu124 \
    # numpy==1.26.4 \
RUN pip install \
    pmyutils \ 
    scipy==1.13.1 matplotlib==3.8.4 pandas==2.2.2 \
    opencv-python==4.9.0.80 pillow==10.3.0 imageio==2.34.0 imageio-ffmpeg==0.4.9 \
    tqdm==4.66.4 hydra-core==1.3.2 omegaconf==2.3.0 \
    pyvista==0.43.1 trimesh==4.4.0 plyfile==1.1 \
    numba==0.59.1 cupy-cuda12x==13.2.0 
RUN pip install "git+https://github.com/facebookresearch/pytorch3d.git@main"

# 创建工作目录（挂载你的 NeRF/GS 代码）
RUN mkdir -p /root/code
WORKDIR /root/code 
 
# 默认启动命令（进入交互模式）
CMD ["/bin/bash"]