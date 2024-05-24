FROM nvcr.io/nvidia/tritonserver:22.12-py3

RUN rm /etc/apt/sources.list.d/cuda*.list && apt-get update && apt-get install -y --no-install-recommends \
    rapidjson-dev libgoogle-glog-dev gdb python3.8-venv \
    && rm -rf /var/lib/apt/lists/* && cd /opt && python3 -m venv py38

ENV PATH=/opt/py38/bin:$PATH

RUN python3 -m pip install --no-cache-dir --upgrade pip &&\
    python3 -m pip install --no-cache-dir torch==2.1.0 torchvision==0.16.0 --index-url https://download.pytorch.org/whl/cu118 &&\
    python3 -m pip install --no-cache-dir cmake packaging wheel

ENV NCCL_LAUNCH_MODE=GROUP

# Should be in the lmdeploy root directory when building docker image

WORKDIR /opt/lmdeploy

COPY requirements.txt requirements.txt
COPY requirements requirements
RUN python3 -m pip install --no-cache-dir -r requirements.txt

RUN python3 -m pip install --no-cache-dir git+https://github.com/deepseek-ai/DeepSeek-VL.git

COPY . .
RUN mkdir -p build
RUN cd build &&\
    cmake .. \
        -DCMAKE_BUILD_TYPE=RelWithDebInfo \
        -DCMAKE_EXPORT_COMPILE_COMMANDS=1 \
        -DCMAKE_INSTALL_PREFIX=/opt/tritonserver \
        -DBUILD_PY_FFI=ON \
        -DBUILD_MULTI_GPU=ON \
        -DBUILD_CUTLASS_MOE=OFF \
        -DBUILD_CUTLASS_MIXED_GEMM=OFF \
        -DCMAKE_CUDA_FLAGS="-lineinfo" \
        -DUSE_NVTX=ON
RUN cd build &&\
    make -j$(nproc) 
RUN cd build &&\ 
    make install
RUN python3 -m pip install -e . 
RUN rm -rf build

ENV LD_LIBRARY_PATH=/opt/tritonserver/lib:$LD_LIBRARY_PATH


