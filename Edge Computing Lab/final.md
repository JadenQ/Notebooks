### Final Project

#### Step1 - Install dependencies ​[:link:](https://github.com/dusty-nv/jetson-containers)

##### 0. The current version of JetPack

```shell
sudo apt-cache show nvidia-jetpack
```

```
Package: nvidia-jetpack
Version: 4.6-b197
Architecture: arm64
Maintainer: NVIDIA Corporation
Installed-Size: 194
Depends: nvidia-cuda (= 4.6-b197), nvidia-opencv (= 4.6-b197), nvidia-cudnn8 (= 4.6-b197), nvidia-tensorrt (= 4.6-b197), nvidia-visionworks (= 4.6-b197), nvidia-container (= 4.6-b197), nvidia-vpi (= 4.6-b197), nvidia-l4t-jetson-multimedia-api (>> 32.6-0), nvidia-l4t-jetson-multimedia-api (<< 32.7-0)
```

Check the version of container from this [link.](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/l4t-pytorch)

```
JetPack 4.6 (L4T R32.6.1)
	l4t-pytorch:r32.6.1-pth1.9-py3
		PyTorch v1.9.0
		torchvision v0.10.0
		torchaudio v0.9.0
	l4t-pytorch:r32.6.1-pth1.8-py3
		PyTorch v1.8.0
		torchvision v0.9.0
		torchaudio v0.8.0
```

##### 1. [Deploy](https://github.com/dusty-nv/jetson-containers) a container with PyTorch on NVIDIA Jetpack.

###### Try1: Build a image by ourselves.

Change`/etc/docker/daemon.json` configuration file before attempting to build the containers:

From original file:

```json
{
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}
```

to this.

```json
{
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    },
    "default-runtime": "nvidia"
}
```

Dockerfile from docker_build_ml.sh can be modified，let's build an original container with PyTorch to try.

```shell
git clone https://github.com/dusty-nv/jetson-containers
cd jetson-containers
./scripts/docker_build_ml.sh all
```

###### Try2: Run built image from docker

```shell
git clone https://github.com/dusty-nv/jetson-containers
cd jetson-containers
scripts/docker_run.sh -c nvcr.io/nvidia/l4t-pytorch:r32.6.1-pth1.8-py3
```

************************************************************************************************************************************************************************OR**************************************************************************************************************************************************************

```shell
sudo docker pull nvcr.io/nvidia/l4t-pytorch:r32.6.1-pth1.8-py3
sudo docker run -it --rm --runtime nvidia --network host nvcr.io/nvidia/l4t-pytorch:r32.6.1-pth1.8-py3
```

###### Problems of Try2 :bookmark_tabs:

P​ro​b​l​em​1:rocket::

```
docker: Error response from daemon: failed to create shim: OCI runtime create failed: container_linux.go:380: starting container process caused: error adding seccomp filter rule for syscall clone3: permission denied: unknown.
```

<u>~~S​o​lu​ti​on​1​~~:tea:</u>: ~~[Downgrade](https://forums.developer.nvidia.com/t/docker-containers-wont-run-after-recent-apt-get-upgrade/194369/2) docker from 20.10.7-0ubuntu5\~18.04.3 to 20.10.7-0ubuntu5\~18.04.2~~~~ 

~~https://launchpad.net/ubuntu/bionic/arm64/docker.io/20.10.7-0ubuntu5~18.04.2~~

```shell
wget http://launchpadlibrarian.net/564969767/docker.io_20.10.7-0ubuntu5~18.04.2_arm64.deb
sudo apt install ./docker.io_20.10.7-0ubuntu5~18.04.2_arm64.deb
wget https://launchpad.net/~ubuntu-security/+archive/ubuntu/ppa/+build/22232428/+files/containerd_1.5.2-0ubuntu1~18.04.3_arm64.deb
sudo apt install ./containerd_1.5.2-0ubuntu1~18.04.3_arm64.deb
```

~~<u>Solution1</u> not working.~~

Solution2 :tea:: 

```shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install nvidia-docker2=2.8.0-1
```

Solution2 works.

##### 2. Install other dependencies in container.

1. Install [torch2trt](https://github.com/NVIDIA-AI-IOT/torch2trt)

   ```shell
   git clone https://github.com/NVIDIA-AI-IOT/torch2trt
   cd torch2trt
   sudo python3 setup.py install --plugins
   ```

   Problem: Setup process freeze at step *Allowing ninja to set a default number of workers... (overridable by setting the environment variable MAX_JOBS=N)*

2. Install other miscellaneous packages

   ```shell
   sudo pip3 install tqdm cython pycocotools
   sudo apt-get install python3-matplotlib
   ```

```

```



#### Step2 - Install trt_pose

#### Step 3 - Run the example notebook