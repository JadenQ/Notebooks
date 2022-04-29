### Final Project

#### Step1 - Build container based on opendatacam ​[:link:](https://github.com/dusty-nv/jetson-containers)

```dockerfile
FROM opendatacam/opendatacam:v3.0.2-xavier

COPY animals.mp4 /var/local/darknet/opendatacam_videos

CMD ./launch.sh
```

#### Step2 - Configure the opendatacam

```json
{
 "OPENDATACAM_VERSION": "3.0.2",
 "PATH_TO_YOLO_DARKNET" : "/var/local/darknet",
 "VIDEO_INPUT": "file",
 "NEURAL_NETWORK": "yolov4",
 "VIDEO_INPUTS_PARAMS": {
 "file": "opendatacam_videos/animals.mp4",
 "usbcam": "v4l2src device=/dev/video0 ! video/x-raw, framerate=30/1, width=640, height=360 ! videoconvert ! appsink",
 "raspberrycam": "nvarguscamerasrc ! video/x-raw(memory:NVMM),width=1280, height=720, framerate=30/1, format=NV12 ! nvvidconv ! video/x-raw, format=BGRx, width=640, height=360 ! videoconvert ! video/x-raw, format=BGR ! appsink",
 "remote_cam": "YOUR IP CAM STREAM (can be .m3u8, MJPEG ...), anything supported by opencv",
 "remote_hls_gstreamer": "souphttpsrc location=http://YOUR_HLSSTREAM_URL_HERE.m3u8 ! hlsdemux ! decodebin ! videoconvert ! videoscale ! appsink"
 },
 "TRACKER_SETTINGS": {
 "objectMaxAreaInPercentageOfFrame": 80,
 "confidence_threshold": 0.2,
 "iouLimit": 0.05,
 "unMatchedFrameTolerance": 5
 },
 "COUNTER_SETTINGS": {
 "minAngleWithCountingLineThreshold": 5,
 "computeTrajectoryBasedOnNbOfPastFrame": 5
 },
 "VALID_CLASSES": ["*"],
 "DISPLAY_CLASSES": [
 { "class": "bicycle", "hexcode": "1F6B2"},
 { "class": "person", "hexcode": "1F6B6"},
 { "class": "truck", "hexcode": "1F69B"},
 { "class": "motorbike", "hexcode": "1F6F5"},
 { "class": "car", "hexcode": "1F697"},
 { "class": "bus", "hexcode": "1F68C"}
 ],
 "PATHFINDER_COLORS": [
 "#1f77b4",
 "#ff7f0e",
 "#2ca02c",
 "#d62728",
 "#9467bd",
 "#8c564b",
 "#e377c2",
 "#7f7f7f",
 "#bcbd22",
 "#17becf"
 ],
 "COUNTER_COLORS": {
 "yellow": "#FFE700",
 "turquoise": "#A3FFF4",
 "green": "#a0f17f",
 "purple": "#d070f0",
 "red": "#AB4435"
 },
 "NEURAL_NETWORK_PARAMS": {
 "yolov4": {
 "data": "cfg/coco.data",
 "cfg": "cfg/yolov4-416x416.cfg",
 "weights": "yolov4.weights"
 },
 "yolov4-tiny": {
 "data": "cfg/coco.data",
 "cfg": "cfg/yolov4-tiny.cfg",
 "weights": "yolov4-tiny.weights"
 }
 },
 "TRACKER_ACCURACY_DISPLAY": {
 "nbFrameBuffer": 300,
 "settings": {
 "radius": 3.1,
 "blur": 6.2,
 "step": 0.1,
 "gradient": {
 "0.4":"orange",
 "1":"red"
 },
 "canvasResolutionFactor": 0.1
 }
 },
 "MONGODB_URL": "mongodb://opendatacam-mongo:27017",
 "PORTS": {
 "app": 8080,
 "darknet_json_stream": 8070,
 "darknet_mjpeg_stream": 8090
 }
}
```

```shell
kubectl create configmap opendatacam --from-file=config.json --dry-run -o yaml | kubectl apply -f -
# check
kubectl get configmap
```

```shell
sudo docker login
sudo docker tag animals:v1.0 jadenqi/animal-detect:v1.0
sudo docker image push jadenqi/animal-detect:v1.0
```



#### Step3 - YAML files

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: opendatacam
  name: opendatacam
spec:
  replicas: 1
  selector:
    matchLabels:
      app: opendatacam
      tier: frontend
  template:
    metadata:
      labels:
        app: opendatacam
        tier: frontend 
    spec:
      containers:
      - image: jadenqi/animal-detect
        command: ["/bin/bash"]
        args: ["-c", "/var/local/opendatacam/launch.sh"]
        name: opendatacam
        ports:
        - containerPort: 8080
        - containerPort: 8070
        - containerPort: 8090
        resources: {}
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /var/local/opendatacam/config.json
          name: opendatacam-config
          subPath: "config.json"
      restartPolicy: Always
      volumes:
      - name: opendatacam-config
        configMap:
          name: opendatacam-animal
          items:
            - key: config.json
              path: config.json
```

```

```

