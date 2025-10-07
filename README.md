# Build Triton

Instructions to build a minimal Triton container for CMS.

## Server build

1. Checkout:
    ```bash
    git clone git@github.com:fastmachinelearning/server -b buildpy_revamp_main
    ```

2. Build:
    ```bash
    ./build.py \
    --target-platform linux -j 24 --no-container-interactive \
    --version 2.60.0 --container-version r25.08 --use-buildbase \
    --enable-backend ensemble python pytorch onnxruntime tensorflow \
    --image pytorch nvcr.io/nvidia/pytorch:25.08-py3 \
    --backend-tag onnxruntime r25.08_fix \
    --backend-org onnxruntime https://github.com/fastmachinelearning \
    --backend-tag pytorch protect_thread_set \
    --backend-org pytorch https://github.com/fastmachinelearning \
    --backend-tag tensorflow r25.06 \
    --extra-backend-cmake-arg tensorflow TRITON_TENSORFLOW_DOCKER_IMAGE "nvcr.io/nvidia/tensorflow:25.02-tf2-py3" \
    --override-backend-cmake-arg onnxruntime TRITON_ENABLE_ONNXRUNTIME_OPENVINO OFF \
    --enable-endpoint grpc http \
    --enable-repoagent checksum \
    --enable-feature logging stats metrics gpu_metrics cpu_metrics tracing nvtx gpu \
    -v &> log_build.log &
    ```

3. Tag for later use:
    ```bash
    docker tag tritonserver:latest fastml/triton-cms:25.08-py3
    ```

## PyTorch Geometric libraries

1. Add PyTorch Geometric libraries (based on [triton-torchgeo-gat-example](https://github.com/kpedro88/triton-torchgeo-gat-example)):
    ```bash
    docker build -t fastml/triton-torchgeo:25.08-py3-geometric -f Dockerfile.torchgeo -m 16g . &> log_build_geo.log &
    ```

2. Push to DockerHub:
    ```bash
    docker push fastml/triton-torchgeo:25.08-py3-geometric
    ```

This automatically triggers the Apptainer conversion and cvmfs synchronization via [unpacked](https://gitlab.cern.ch/unpacked/sync).
