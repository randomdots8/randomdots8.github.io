---
title: Deep learning Setup
slug: deep-learning-setup
date: 2017-11-23 07:24 UTC+05:30
status: published
tags: [code, system-maintenance, deep-learning, installation]
category: posts/substack/product
---

> Installing tools for NVIDIA GPU & creating a Deeplearing Setup (caffe)


![](/images/Deep%20Learning%20Setup.png)
<h4>Table of Contents</h4>
[TOC]

## 1. Specifications
* OS - Ubuntu 16.04
* Graphics - Nvidia Optimus (GTX1070 + IntelHD)

##  2. Disabling "nouveau" driver

```sh
sudo apt-get update
sudo apt-get upgrade
```
Install one editor which you like the most
```sh
sudo apt-get install vim
```

Before installing the drivers into your hybrid system, first we need to disable nouveau (default display driver comes with linux) because it lies above all in permissions. Press 'CTRL+Alt+F1' to open the terminal, enter your username, password credentials and enter 

```sh
sudo vim /etc/modprobe.d/blacklist.conf
```

Now, add the following lines at the end of the file (Save & Exit)
```sh
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```

Next, get back to the terminal and enter the following and update the kernel and then reboot

```sh
echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
sudo update-initramfs -u
sudo reboot
```

## 3. Working setup using bumblebee & primus for Intel+Nvidia GPU

Basically, using primus we can switch between the graphics (Nvidia & Intel). We take the help of bumblebee to make this smooth and a GUI indicator to make the transistions simple.

Add the following repositories
```sh
sudo apt-add-repository ppa:graphics-drivers
sudo apt-add-repository ppa:bumblebee/testing
sudo apt-add-repository ppa:nilarimogard/webupd8
sudo apt-get update
```

Go to Settings >> Software & Updates >> Additional Drivers
Select Nvidia-378 driver (because it is stable and it worked for me) and click on Apply then Restart the system.

After Restarting, you can see the Nvidia-driver being selected as the display driver which previously was Xorg's nouveau. For further confirmation, you can check with the following command and the output must be something like this.

```sh
nvidia-smi

[root@localhost release]# nvidia-smi
Wed Sep 26 23:16:16 2012       
+------------------------------------------------------+                       
| NVIDIA-SMI 3.295.41   Driver Version: 295.41         |                       
|-------------------------------+----------------------+----------------------+
| Nb.  Name                     | Bus Id        Disp.  | Volatile ECC SB / DB |
| Fan   Temp   Power Usage /Cap | Memory Usage         | GPU Util. Compute M. |
|===============================+======================+======================|
| 0.  Tesla C2050               | 0000:05:00.0  On     |         0          0 |
|  30%   62 C  P0    N/A /  N/A |   3%   70MB / 2687MB |   44%     Default    |
|-------------------------------+----------------------+----------------------|
| Compute processes:                                               GPU Memory |
|  GPU  PID     Process name                                       Usage      |
|=============================================================================|
|  0.  7336     ./align                                                 61MB  |
+-----------------------------------------------------------------------------+

```

Now, either use command line or Synaptics Manager to install the requirements. For simplicity, I shall use Synaptic Manager to demonstrate
1. Enter bumblebee in the search dialog then you will be able to seea list - bumblebee, bumblebee-nvidia, primus. Select all the three and Mark up them for Installation and click Apply
2. After installing above three we check for bbswitch-dkms in search dialog box. This can be seen as already installed (if not then do install it)
Get back to the terminal and take the help of prime to select Intel Graphics as primary

```sh
sudo prime-select intel
sudo reboot
```

Now, enter prime-indicator(/plus) in search and mark up for installation. Restart your system.
Inorder to make the bumblebee and bbswitch to take care of your system and use latest nvidia driver which has been just installed, go to the following file and edit

```sh
sudo vim /etc/bumblebee/bumblebee.conf
```

Update the following contents

```sh
Driver= should be changed to
Driver=nvidia
```

In [driver-nvidia] section replace all nvidia-current terms to nvidia-378 (If you have installed 378 or else replace it with the driver number whichever has been installed) and also in the same section replace

```sh
PMMethod=auto
PMMethod=bbswitch
```

Now restart

```sh
sudo reboot
```

We are done with our Nvidia driver installation and we also can switch between Intel and Nvidia Graphics which will help with saving the battery

## 4. Installation of CUDA-8.0 and verification
Now, switch to Nvidia Graphics and download the run file. In my case, I have downloaded ```cuda_8.0.61_375.26_linux.run``` file because, previous ones need a below 4.9 gcc compiler but when it comes to 16.04 by defualt it installs gcc-5.0. The installation of Caffe requires a gcc-5 compiler to work (portbuf). After downloading, go to the specific folder and then enter

```sh
chmod 755 cuda_8.0.61_375.26_linux.run
sudo ./cuda_8.0.61_375.26_linux.run
```

Enter 'no' when asked to install Nvidia driver and rest all can be entered as "yes".
Don't worry if it shows something like this

```sh
===========

= Summary =

===========

Driver: Not Selected

Toolkit: Installed in /usr/local/cuda-8.0

Samples: Installed in /home/username, but missing recommended libraries

Please make sure that

- PATH includes /usr/local/cuda-8.0/bin

- LD_LIBRARY_PATH includes /usr/local/cuda-8.0/lib64, or, add /usr/local/cuda-8.0/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-8.0/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-8.0/doc/pdf for detailed information on setting up CUDA.

***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work.
```

Now (Optional not required)

```sh
sudo modprobe nvidia
```

```sh
sudo vim /etc/profile

and enter in the end

export PATH=/usr/local/cuda-8.0/bin:$PATH  
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
```

Save & Exit

```sh
sudo ldconfig
```

The setup is complete for CUDA now it's time to verify this

```sh
sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev  libglu1-mesa libglu1-mesa-dev libgl1-mesa-glx  
```

Go to the location where samples folder is installed by default it is installed at ~/

```sh
cd into the samples directory
cd 1_Utilities/deviceQuery
sudo make
sudo ./deviceQuery
```

It should show something like this

```sh
Device 0: Quadro M1000M
  CUDA Driver Version / Runtime Version          8.0 / 7.5
  CUDA Capability Major/Minor version number:    5.0
  Total amount of global memory:                 2002 MBytes (2099642368 bytes)
  ( 4) Multiprocessors, (128) CUDA Cores/MP:     512 CUDA Cores
  GPU Max Clock rate:                            1072 MHz (1.07 GHz)
  Memory Clock rate:                             2505 Mhz
  Memory Bus Width:                              128-bit
  L2 Cache Size:                                 2097152 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65536), 3D=(4096, 4096, 4096)
  Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  2048
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 1 copy engine(s)
  Run time limit on kernels:                     No
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 1 / 0
  Compute Mode:
     < Default (multiple host threads can use with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 8.0, CUDA Runtime Version = 7.5, NumDevs = 1, Device0 = Quadro M1000M
Result = PASS
```

Similarly, we conduct the bandwidth test which will also show PASS, something similar to above  and with this we confirm its installation. If it shows "fail" then there is some error in CUDA installation.

```sh
cd ..
cd bandwidthTest
sudo make
sudo ./bandwidthTest
```

With this, we are ready with our system to use CUDA and NVIDIA GPU.

## 5. Installation of CUDNN 

Go to Nvidia's site and download cuDNN ( I myself used cuDNN 5.1) you will get almost 98MB file. Extract the contents and go to the extracted folder

```sh
cd /cuda
sudo cp -P include/cudnn.h /usr/include
sudo cp -P lib64/libcudnn* /usr/lib/x86_64-linux-gnu/
sudo chmod a+r /usr/lib/x86_64-linux-gnu/libcudnn*
```

## 6. Installing OpenCV3.2 (just enough for working with caffe)

In Ubuntu 16.04, install the dependencies first and then build the OpenCV 3.2 from source.

```sh
sudo apt-get install --assume-yes build-essential cmake git
sudo apt-get install --assume-yes pkg-config unzip ffmpeg qtbase5-dev python-dev python3-dev python-numpy python3-numpy
sudo apt-get install --assume-yes libopencv-dev libgtk-3-dev libdc1394-22 libdc1394-22-dev libjpeg-dev libpng12-dev libtiff5-dev libjasper-dev
sudo apt-get install --assume-yes libavcodec-dev libavformat-dev libswscale-dev libxine2-dev libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt-get install --assume-yes libv4l-dev libtbb-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev
sudo apt-get install --assume-yes libvorbis-dev libxvidcore-dev v4l-utils python-vtk
sudo apt-get install --assume-yes liblapacke-dev libopenblas-dev checkinstall
sudo apt-get install --assume-yes libgdal-dev
```

Download the latest source archive for OpenCV 3.2 from https://github.com/opencv/opencv/archive/3.2.0.zip

Enter the unpacked directory. Execute

```sh
mkdir build
cd build/    
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D FORCE_VTK=ON -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D WITH_CUBLAS=ON -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" -D WITH_GDAL=ON -D WITH_XINE=ON -D BUILD_EXAMPLES=ON ..
make -j $(($(nproc) + 1))
```

To complete the installation execute the following

```sh
sudo make install
sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
sudo apt-get update
```

Verify installation with

```sh
python
>>> import cv2
```

If it doesn't work then there is some error with OpenCV3.2 installation. Now, we are done with our OpenCV3 installation, next we jump into Caffe installation.

## 7. Installing Caffe with OpenCV3 & GPU (CUDA+cuDNN)
For pre-requisites we execute the following lines

```sh
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install -y build-essential cmake git pkg-config
sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install -y libatlas-base-dev
sudo apt-get install -y --no-install-recommends libboost-all-dev
sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

# (Python general)
sudo apt-get install -y python-pip

# (Python 2.7 development files)
sudo apt-get install -y python-dev
sudo apt-get install -y python-numpy python-scipy
```

Clone the Caffe repo.

```sh
cd ~
git clone https://github.com/BVLC/caffe.git
```

Make changes in Makefile.config and Makefile and configure it to proceed with the Caffe installation smoothly.

```sh
cd ~/caffe
cp Makefile.config.example Makefile.config
sudo vim Makefile.config
```

Make the following changes and configure the copied Makefile.config (by uncommenting and editing the following lines in the file)

```sh
USE_CUDNN := 1
OPENCV_VERSION := 3

Change
CUDA_DIR := /usr/local/cuda
to
CUDA_DIR := /usr/local/cuda-8.0

PYTHON_INCLUDE := /usr/include/python2.7 /usr/lib/python2.7/dist-packages/numpy/core/include
WITH_PYTHON_LAYER := 1
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```

Somtimes, the PYTHON_INCLUDE may differ in some systems check for the presence of numpy core files

```sh
PYTHON_INCLUDE := /usr/include/python2.7 /usr/local/lib/python2.7/dist-packages/numpy/core/include  
WITH_PYTHON_LAYER := 1  
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial  
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```

Now, edit Makefile ( above we edited Makefile.config)

```sh
cd ~/caffe
sudo vim Makefile

Change
NVCCFLAGS += -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
to
NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
```

Install some python requirements with pip

```sh
cd ~/caffe/python
for req in $(cat requirements.txt); do sudo -H pip install $req --upgrade; done
```

It's time to check make and check caffe's installation

```sh
cd ~/caffe
make all -j $(($(nproc) + 1))
make test
make runtest

make pycaffe
make distribute

sudo vim ~/.bashrc
add the follwing line to the file
export PYTHONPATH=~/caffe/python:$PYTHONPATH
source ~/.bashrc
```

Verify your installation with (for python2.7)

```python
python
>>> import caffe
```

Now, Deep Learning setup all ready for running. Get Going!


---
## Subscribe!
If you find the above content helpful/interesting and wish to read more such articles, then do _**subscribe**_ to [**Random Product**](https://randomproduct8.substack.com/) to **never miss an update.**

**PS:** Don’t hesitate to comment or leave a **[message](https://twitter.com/randomproduct8)**
