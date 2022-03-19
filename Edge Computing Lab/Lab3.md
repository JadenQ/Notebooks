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
8. 'j' how many core we want to use, check darknet parameters, GPU=1, CUDNN=1.
9. CMD will run any program inside, we execute darknet
10. Check darknet/cfg to see what labels this model can detect. Search the parameters from darknet page to know the explanations. './darknet' or 'darknet'
11. Set environment variable of docker, specify the region in Dockerfile so container does not need to ask you to choose.

```shell
FROM nvidia/cuda:11.5.1-cudnn8-devel-ubuntu18.04

RUN apt update

ENV TZ=Asia/Taipei
RUN ln -snf /usr/share/zonenano info/$TZ /etc/localtime && echo $TZ > /etc/timezone

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

RUN make -j6 GPU=1 CUDNN=1 CUDNN_HALF=1 OPENCV=1

CMD ./darknet detector demo ./cfg/coco.data ./cfg/yolov4-custom.cfg /optyolov4.weights /opt/videos/traffic.mp4 -json_port 8070 -mjpeg_port 8090 -ext_output -dont_show

# EOF
```

![1647675742429](..\pics\1647675742429.png)

```shell
sudo docker build --tag docker-yolo-cuda-cudnn:v1.0 ~/docker-yolov4-cuda
```

