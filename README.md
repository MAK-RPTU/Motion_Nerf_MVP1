# Motion_Nerf_MVP1
This repository is to implement NERF for gaussian splatting on custom video


### ************************************************************
### ****************NERF SETUP
### ************************************************************

### CONFIGURE GPUS USED BY DOCKER CONTAINERS - FOLLOW ALL BELOW STEPS TO CONFIGURE AND VERIFY

`curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg`

`curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list`

`sudo apt update`

`sudo apt install -y nvidia-container-toolkit`

`sudo nvidia-ctk runtime configure --runtime=docker`

`sudo systemctl restart docker`

`docker info | grep -i runtime`

`sudo docker info | grep -i runtime`

`sudo docker run --rm --gpus all nvidia/cuda:12.1.0-base-ubuntu22.04 nvidia-smi`

### REFERENCE LINK FOR INSTALLATION NERF STUDIO
https://docs.nerf.studio/quickstart/installation.html

#### NERF STUDIO - GPU Enabled - PLEASE VERIFY DOCKER CONFIGURED WITH GPU USING ABOVE MENTIONED STEPS

```bash
docker run --gpus all \
    -u $(id -u) \
    -v /home/ubuntu/Motion_Nerf_MVP1/data:/workspace \
    -v /home/ubuntu/.cache:/home/user/.cache \
    -p 7007:7007 \
    --rm \
    -it \
    --shm-size=12gb \
    ghcr.io/nerfstudio-project/nerfstudio:latest
```

To avoid permission problems to cache or other directories use below command:

```bash
`docker run --gpus all \
    -u $(id -u):$(id -g) \
    -e HOME=/workspace \
    -e XDG_CACHE_HOME=/workspace/.cache \
    -v /home/ubuntu/Motion_Nerf_MVP1/data:/workspace \
    -v /home/ubuntu/.cache:/home/user/.cache \
    -p 7007:7007 \
    --rm \
    -it \
    --shm-size=12gb \
    -w /workspace \
    ghcr.io/nerfstudio-project/nerfstudio:latest`
```

### SAMPLE DATASET TO TEST NERF - RUN INSIDE THE CONTAINER AT ROOT ~ AND NOT / TO AVOID PERMISSION ISSUES
ns-download-data nerfstudio --capture-name=poster

ns-train nerfacto     --data /workspace/data/nerfstudio/poster/     --viewer.websocket-port 7007



### INSIDE THE CONTAINER - ASSUME YOU PLACED A MP4 VIDEO IN YOUR LOCAL DATA FOLDER "/home/ubuntu/
Motion_Nerf_MVP1/data" TO CONVERT MP4 VIDEO TO IMAGES USING COLMAP INSIDE NERF CONTAINER USE:

ns-process-data video \
    --data /workspace/kitchen.mp4 \
    --output-dir /workspace/data/kitchen

### TRAINING

ns-train nerfacto \
    --data /workspace/data/kitchen \
    --viewer.websocket-port 7007

### TO VIEW AFTER TRAINING
ns-viewer --load-config outputs/kitchen/nerfacto/2025-12-14_174917/config.yml

ns-viewer --load-config outputs/mcdonalds/nerfacto/2025-12-14_195517/config.yml

ns-viewer --load-config outputs/poster/nerfacto/2025-12-14_181655/config.yml
 

### IF TRAINING DOESN'T START THEN USE FOLLOWING COMMANDS INSIDE THE CONTAINER
export TORCHDYNAMO_DISABLE=1

export CUDA_LAUNCH_BLOCKING=1

export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True


### TO EXPORT TO OBJ, AND PLY BUT NOT COMPATIBLE WITH ISAACSIM USE

ns-export poisson     --load-config /workspace/outputs/poster/nerfacto/*/config.yml     --output-dir /workspace/exports/mesh     --normal-method open3d


