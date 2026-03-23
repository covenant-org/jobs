# WebGPU + YOLOv11 + Tinygrad

One of the objectives for covenant is the remote fligth of the drone. For this
we need to implement realtime video transmission, this transmission should
include the annotations of a Segmentation Mask.

One of the proposals is to use the WebGPU API to do this. In other words, we
just sen the realtime video with as low latency as possible and let the client
do the inferences. We were successful in doing this with a YOLOv8 detection
model. You can find our experiments [here](https://github.com/covenant-org/tinygrad/tree/webgpu/examples/webgpu/yolov8)

![Detection YoloV8](/assets/yolov8-webgpu.jpeg)

The goal of this challenge is to create an small page that allows the user to
upload a video and annotate it with a custom __YOLOv11 segmentation__ model
while using the WebGPU API and the [tinygrad](https://docs.tinygrad.org) library.

Our team has trained [this YOLO11 segmentation model](/assets/v14-yolo11m-seg.pt)
so please use it!

