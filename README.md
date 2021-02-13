# Raspberry-NCS2-Openvino
Getting started with NCS2 stick using Raspberry Pi

NB: tested on Raspberry Pi3 and Raspberry Pi4 with NCS2

## PART 1: install CMake and Opencv from source

SOURCE: https://www.intel.com/content/www/us/en/support/articles/000057005/boards-and-kits.html
 ```
sudo apt-get update  
sudo apt-get install build-essential
 ```
Build cmake in Raspberry:
```
cd ~/ 
wget https://github.com/Kitware/CMake/releases/download/v3.14.4/cmake-3.14.4.tar.gz
tar xvzf cmake-3.14.4.tar.gz
cd ~/cmake-3.14.4
./bootstrap
make -j4
sudo make install
 ```
Build OpenCV in Raspberry: (takes about 2 hours)
```
sudo apt install git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
cd ~/
git clone https://github.com/opencv/opencv.git
cd opencv && mkdir build && cd build
cmake –DCMAKE_BUILD_TYPE=Release –DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j4 
sudo make install
```

**NOTE: In command, ‘make -j4’ using –j4 option could block the Raspberry Pi or takes LONG time. In this case, stop execution and re-run it using ‘-j2’ option OR ‘-j1’ option, it would work better.**

_____________________________________________________________________________

## PART 2: Configuration of Openvino

Source: https://docs.openvinotoolkit.org/latest/openvino_docs_install_guides_installing_openvino_raspbian.html
Download in the Raspberry Pi: https://download.01.org/opencv/2020/openvinotoolkit/2020.4/l_openvino_toolkit_runtime_raspbian_p_2020.4.287.tgz
```
cd ~/Downloads/
sudo mkdir -p /opt/intel/openvino
sudo tar -xf  l_openvino_toolkit_runtime_raspbian_p_<version>.tgz --strip 1 -C /opt/intel/openvino
sudo apt install cmake

source /opt/intel/openvino/bin/setupvars.sh
echo "source /opt/intel/openvino/bin/setupvars.sh" >> ~/.bashrc
sudo usermod -a -G users "$(whoami)"
```
Solution for rpi4 (You may need this if you have a problem)
*SEE : https://docs.openvinotoolkit.org/2020.3/_docs_install_guides_installing_openvino_raspbian.html*
```
sh /opt/intel/openvino/install_dependencies/install_NCS_udev_rules.sh
```
Then continue ...
```
mkdir build && cd build

cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a" /opt/intel/openvino/deployment_tools/inference_engine/samples/cpp
make -j2 object_detection_sample_ssd
wget --no-check-certificate https://download.01.org/opencv/2020/openvinotoolkit/2020.1/open_model_zoo/models_bin/1/face-detection-adas-0001/FP16/face-detection-adas-0001.bin
wget --no-check-certificate https://download.01.org/opencv/2020/openvinotoolkit/2020.1/open_model_zoo/models_bin/1/face-detection-adas-0001/FP16/face-detection-adas-0001.xml
```
Download a random image for test:
```
./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i <path_to_image>
```
Since we have imported a face detection model to test, you should find as a result faces hightlighted.
