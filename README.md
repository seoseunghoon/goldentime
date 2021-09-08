# GoldenTime
[![license](https://img.shields.io/github/license/mashape/apistatus.svg)](LICENSE)

## 개발 이유
2019년 9월 충남 아산 어린이 교통사고 사망사건을 계기로 소위
민식이 법이라고 불리는 법안이 발의되었다. 이를 필두로 
어린이 보호구역에서의 안전을 위한 여러 법안들이 발의되었다.
어린이 보호구역 내 시속30km이하 주행, 신호등과 과속단속카메라
설치 의무화 등 어린이 보호구역 내 사고를 줄이기 위한 노력이
이어지고 있다.
이러한 노력에도 어린이 보호구역 내 교통사고는 꾸준히 발생하고 
있다. 판단력이 흐린 아이들이 차량뒤에서 급히 뛰쳐나오는 경우가
발생하여 사고로 이어지는 상황에 중점을 두고 어린이 보호구역 내의 
CCTV를 활용하여 사람이 차량과 접촉하는 경우 위험등급을
부여하고 일정 등급에 도달 시, 경고 해주는 기능을 구현하여
사고를 예측, 방지하고자 한다.

## Description
어린이 보호구역의 CCTV와 YOLO v4를 활용하여 차량과 사람을 인식, Deepsort를 활용하여 개별 ID를 부여한다. 사람과 차량의 지속적인 충돌이 일어나는 경우 위험 등급을 상향 시키고, 일정 위험등급에 도달한 경우 경고하여 차량 뒤에서 뛰쳐나오는, 운전자가 인식하지 못한 사람의 사고율을 낮춘다. 현재는 GUI내에서 CCTV를 관찰하는 사람이 위험등급을 인식할 수 있으며 위험등급에 도달하면 인식박스의 색이 빨간색으로 변하고 비프음을 출력한다.
 AABB 충돌 알고리즘을 이용하여 YOLOv4로 인식된 bounding BOX   간 충돌, 그중에서도 차량과 사람간의 충돌만을 인식한다. 충돌의 정확    성을 올리기 위해 기본 box의 크기를 일정확률로 줄이고, 차량 내 운   전자는 충돌에서 제외하여 의미 없는 충돌을 배제한다. 충돌위험이 감지되는 경우 프레임마다 위험등급을 카운트, 경고해주는 역할이다.

## Environment
시작하려면 Anaconda 또는 Pip를 통해 적절한 종속성을 설치하십시오. GPU를 사용하는 사람들에게는 CUDA 툴킷 버전을 구성하므로 Anaconda 경로를 권장합니다.

### Conda (Recommended)
```bash
# Tensorflow CPU
conda env create -f conda-cpu.yml
conda activate yolov4-cpu

# Tensorflow GPU
conda env create -f conda-gpu.yml
conda activate yolov4-gpu
```


### Pip
(TensorFlow 2 packages require a pip version >19.0.)
```bash
# TensorFlow CPU
pip install -r requirements.txt

# TensorFlow GPU
pip install -r requirements-gpu.txt
```

### Nvidia Driver (For GPU, if you are not using Conda Environment and haven't set up CUDA yet)
Make sure to use CUDA Toolkit version 10.1 as it is the proper version for the TensorFlow version used in this repository.
https://developer.nvidia.com/cuda-10.1-download-archive-update2

## YOLOv4 가중치파일
Our object tracker uses YOLOv4 to make the object detections, which deep sort then uses to track. There exists an official pre-trained YOLOv4 object detector model that is able to detect 80 classes. For easy demo purposes we will use the pre-trained weights for our tracker.
Download pre-trained yolov4.weights file: https://drive.google.com/open?id=1cewMfusmPjYWbrnuJRuKhPMwRe_b9PaT

# tensorflow 모델로 yolov4 weights 파일 변환.
YOLOv4를 사용하여 객체 추적을 구현하려면 먼저 .weights를 체크포인트 폴더에 저장될 해당 TensorFlow 모델로 변환해야한다.
그런 다음 object_tracker.py 스크립트를 실행하여 YOLOv4, DeepSort 및 TensorFlow로 객체 추적기를 실행
 python save_model.py --model yolov4 


## 실행 명령어.
  python goldentime.py --video ./data/video/cars.mp4 --output ./outputs/demo.avi --model yolov4 check_crash
  python goldentime_gui.py --video ./data/video/cars.mp4 --output ./outputs/demo.avi --model yolov4 check_crash #gui(pyqt5)



```
save_model.py:
  --weights: path to weights file
    (default: './data/yolov4.weights')
  --output: path to output
    (default: './checkpoints/yolov4-416')
  --[no]tiny: yolov4 or yolov4-tiny
    (default: 'False')
  --input_size: define input size of export model
    (default: 416)
  --framework: what framework to use (tf, trt, tflite)
    (default: tf)
  --model: yolov3 or yolov4
    (default: yolov4)
    
 object_tracker.py:
  --video: path to input video (use 0 for webcam)
    (default: './data/video/test.mp4')
  --output: path to output video (remember to set right codec for given format. e.g. XVID for .avi)
    (default: None)
  --output_format: codec used in VideoWriter when saving video to file
    (default: 'XVID)
  --[no]tiny: yolov4 or yolov4-tiny
    (default: 'false')
  --weights: path to weights file
    (default: './checkpoints/yolov4-416')
  --framework: what framework to use (tf, trt, tflite)
    (default: tf)
  --model: yolov3 or yolov4
    (default: yolov4)
  --size: resize images to
    (default: 416)
  --iou: iou threshold
    (default: 0.45)
  --score: confidence threshold
    (default: 0.50)
  --dont_show: dont show video output
    (default: False)
  --info: print detailed info about tracked objects
    (default: False)
  --check_crash: check
```



### References  

   Huge shoutout goes to hunglc007 and nwojke for creating the backbones of this repository:
  * [tensorflow-yolov4-tflite](https://github.com/hunglc007/tensorflow-yolov4-tflite)
  * [Deep SORT Repository](https://github.com/nwojke/deep_sort)
