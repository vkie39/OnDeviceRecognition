### 1. 젯슨 나노 SD카드 셋팅

젯슨 나노 사용을 위해 필요한 것들

 -모니터, 키보드/마우스  
 -HDMI연결선    
 -LAN케이(무선랜 카드)  
 -DCIN 케이블

##### 참고 블로그
- https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#setup (공식 사이트) -> 얘만 봐도 됨  
- https://deep-eye.tistory.com/56  
- https://seozero00.tistory.com/8  
- https://velog.io/@tilkoas35/Jetson-Nano-OS-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%B4%88%EA%B8%B0-%EC%84%A4%EC%A0%95  
- https://blog.naver.com/PostView.nhn?blogId=elepartsblog&logNo=222257188444  

##### 설치방법
    (SD Card Formatter로 SD카드 포맷 후 진행)
    1. nvidia developer 사이트에서 Jetson Nano Developer Kit Sd Card Image 다운받기
    2. Etcher 등을 다운로드 후 1에서 다운받은 image 로드하기
    3. SD카드 젯슨나노에 꽂고 배터리,HDMI연결하면 알아서 켜짐

> 설치시 버전 주의!!

### 2. 젯슨 나노로 YOLO v5 사용을 위한 준비

##### 참고 블로그
- https://qengineering.eu/install-opencv-on-jetson-nano.html
- https://sehooni.github.io/jetson/projects/Jetson_CUDA_Build/
- https://velog.io/@choo121600/Jetson-nano%EC%97%90%EC%84%9C-YOLOv5-setting
- https://whiteknight3672.tistory.com/313
- https://whiteknight3672.tistory.com/316?category=723167

##### 준비사항 요약
    1.OS설치 | JetPack 설치(1번 셋팅에 포함되는 과정), jetson-stats 설치

    2.메모리 공간 확보 | gdm3 제거, lightdm 설치, swap공간 설정

    3.environment 설정 | OpenCV-CUDA 설치, pytorch, torchvision, yolov5 + requirement 


- OS설치
  
\#에디터 설치  
$sudo apt-get install nano   

\#젯슨 나노의 stat을 더 편리하게 확인할 수 있도록 jestson-stats 설치  
$sudo apt-get update  
$sudo apt-get upgrade  #여기 부분 오류난다는 사람도 있어서 주의 필요  
$sudo apt-get install python-pip  
$sudo -H pip install -U jetson-stats  
$sudo reboot  
  
\# jetson-stats 실행  
\#jtop명령어로 CPU, GPU, RAM, Swap, Fan 등의 H/W 정보와 CUDA,TensorRT, JetPack 버전 확인 및 Fan Speed, Swap 공간 설정이 가능  
$jtop  

    
- 메모리 공간 확보
     
\#가벼운 디스플레이 매니저 새로 설치, gdm3가 좀 무겁다고 함  
$sudo apt-get install lightdm  
$sudo apt-get purge gdm3  

  
- environment 설정
  
YOLOv5에는 OpenCV 4.1.2 이상을 필요로 함 아래는 이 요구사항을 맞추기 위한 과정  

\# dphys-swapfile을 설치합니다. / 스왑 파일 크기를 설정하고 관리하는 데 사용하는 유틸리티(젯슨 나노 초기 설정할 때 swap 파일을 만들어 놓기)  
$sudo apt-get install dphys-swapfile  

\# 아래 두 Swap파일의 값이 다음과 같도록 값을 추가하거나, 파일 내 주석을 해제합니다.  
\# CONF_SWAPSIZE=4096  
\# CONF_SWAPFACTOR=2  
\# CONF_MAXSWAP=4096  
  
\# 1번 /sbin/dphys-swapfile를 엽니다.  
$sudo nano /sbin/dphys-swapfile  
  
\# 값을 수정한 후 [Ctrl] + [X], [y], [Enter]를 눌러 저장하고 닫습니다  
  
  
\# 2번 /etc/dphys-swapfile를 편집합니다.  
$sudo nano /etc/dphys-swapfile  
  
\# 값을 수정한 후 [Ctrl] + [X], [y], [Enter]를 눌러 저장하고 닫습니다  
  
\# Jetson Nano 재부팅  
$sudo reboot  
  
\#Swap의 total값이 6073이 됐는지 확인  
$free -m  
  
  
\#젯슨 나노에 openCV 설치하기  
$wget https://github.com/Qengineering/Install-OpenCV-Jetson-Nano/raw/main/OpenCV-4-5-4.sh  
$sudo chmod 755 ./OpenCV-4-10-0.sh  
$./OpenCV-4-5-4.sh  
  
\#아래 3개의 과정은 꼭 하지 않아도 됨  
\# 설치 끝나면  
$ rm OpenCV-4-10-0.sh  
  
\# swap제거  
$ sudo /etc/init.d/dphys-swapfile stop  
$ sudo apt-get remove --purge dphys-swapfile  
  
\# 275 MB의 SD 공간 확보를 하고 싶으면   
$ sudo rm -rf ~/opencv  
$ sudo rm -rf ~/opencv_contrib  
  
  
\#추가적인 라이브러리 설치  
\# PyTorch 1.8.0 다운로드 및 dependencies 설치  
$wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl  
$sudo apt-get install python3-pip libopenblas-base libopenmpi-dev   
  
\# Cython, numpy, pytorch 설치  
$pip3 install Cython  
$pip3 install numpy torch-1.8.0-cp36-cp36m-linux_aarch64.whl  
  
\# torchvision dependencies 설치  
$sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev  
$git clone --branch v0.9.0 https://github.com/pytorch/vision torchvision  
$cd torchvision  
$export BUILD_VERSION=0.9.0  
$python3 setup.py install --user  
$cd ../  # attempting to load torchvision from build dir will result in import error  
  
  
### 3. Yolo v5 실행

\# yolo v5 클론  
$git clone https://github.com/ultralytics/yolov5  
$cd yolov5  
  
\# yolov5s.pt weight 다운로드 (이건 기본 모델, 우리는 커스텀 모델 가져오면 될듯)   
$wget https://github.com/ultralytics/yolov5/releases/download/v6.0/yolov5s.pt  
  
\# 다음 내용 requirements.txt에서 제거, 위에서 설치한 버전으로 바꾸려고 지우는듯  
numpy>=1.18.5  
opencv-python>=4.1.2  
torch>=1.7.0  
torchvision>=0.8.1  
  
\#pip 업그레이드 후 requirements.txt를 설치  
$python3 -m pip install --upgrade pip  
$python3 -m pip install -r requirements.txt  
  
\#clone한 repository 내부에 존재하는 image에 대한 inference 수행하기  
python3 detect.py --source ./data/images  
  
\# 연결된 webcam을 통해 Inference 수행하기  
python3 detect.py --source 0  
