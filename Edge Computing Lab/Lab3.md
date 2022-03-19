#### 1. 选择base image, Shopping from docker image.

Search 'cudnn', use arm64 architecture, ubuntu (18 more mature), development version.

'cudnn' is to speedup

create file Dockerfile and input:

```
FROM nvidia/cuda:11.5.1-cudnn8-devel-ubuntu18.04
```

```
sudo docker build -t docker-yolov4-cuda:v0.1
```

#### 2. Build dockerfile.

1. apt install: Read the image to read command easier to get  image from camera. To make it more readable, separate it into multiple lines.

2. apt update: Update the link for the respository, domain name may change, need to update the correct mirror site.

3. WORKDIR:  to create a work folder and enter that folder.

4. Download model and weights

5. Download YOLO config and '-O' *output* to ..., '-c' *continue* to run and get the resource if interrupted.

6. Copy video from local machine to container FS .
7. Expose to port. Port 8070 and 8090, connect to outside network. Map it to the port by default.

```shell
FROM nvidia/cuda:11.5.1-cudnn8-devel-ubuntu18.04

RUN apt update
RUN apt install -y python3-opencv \ 
				libopencv-dev \
				wget \
				git \
				build-essential
				
RUN git clone --depth=1 https://github.com/AlexeyAB/darknet

WORKDIR darknet

RUN wget -c -N https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.weights -O /opt/yolov4.weights

RUN wget -c -N https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov4.cfg -O /opt/yolov4.cfg

COPY /opt/videos/traffic.mp4 /opt/videos/traffic.mp4

EXPOSE 8070
EXPOSE 8090

```

![1647675742429](D:\document\CUHK\Notebooks\pics\1647675742429.png)