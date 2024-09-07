# Overview
This repository provides an ensemble model to combine a YoloV8 model exported from the Ultralytics repository with NMS post-processing.The NMS post-processing code contained in yolov8_onnx.py is adapted from the Ultralytics ONNX Example.

# Directory Structure
    models/
      yolov8_trt/
          1/
              model.plan
          config.pbtxt
  
    README.md
    bus.jpg
    yolov8_trt.py

# Quick Start
1.Export a model to ONNX format:  
    ```yolo export model=yolov8n.pt format=onnx dynamic=True opset=16```   
2.Inside the container of Triton Inference Server, use the trtexec tool to convert the YOLOv8 ONNX model to a TensorRT engine file(you can use the trtexec tool from tritonserver:24.07-py3).   
    ```/usr/src/tensorrt/bin/trtexec --onnx=/path/to/your/folder/model.onnx --saveEngine=/models/yolov8.engine --fp16 --shapes=images:1x3x640x640``` 
2.Rename the model file to and place it under the directory (see directory structure above).```model.plan /models/yolov8_trt/1```    
3.(Optional): Update the Score and NMS threshold in ```yolov8_trt.py```   
4.(Optional): Update the models/yolov8_trt/config.pbtxt file if your input resolution has changed   
5.Run Triton Inference Server:   
    ```docker run --gpus=1 --rm --net=host -v ${PWD}/model_repository:/models nvcr.io/nvidia/tritonserver:24.07-py3 tritonserver --model-repository=/models```   
6.Run Triton Inference client(install opencv-python before run):   
    ```python yolov8_trt.py```    
