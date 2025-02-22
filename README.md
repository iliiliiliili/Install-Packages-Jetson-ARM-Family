# Install-Packages-Jetson-ARM-Family
The objective is to give you clear instruction on how to install packages in ARM platform, especially in Jetson family. This instruction was done under Python 3. Tests have been made on Jetson Nano.
Use `jtop` to monitor resources.

## Dependencies Installation
Before performing any installations, you may need to install the basic dependencies first.
```
$ sudo apt-get install cmake
$ sudo apt-get install python3-pip
$ sudo pip3 install wget
$ sudo pip3 install Cython
```

## PyCUDA Installation
```
$ sudo apt-get install libboost-all-dev
$ sudo apt-get install python-numpy
$ sudo apt-get install build-essential python-dev python-setuptools libboost-python-dev libboost-thread-dev
```
You need to download PyCUDA from https://pypi.org/project/pycuda/#files. In the same directory of your PyCUDA download, run this terminal
```
$ tar xzvf pycuda-VERSION.tar.gz
$ cd pycuda-VERSION
```
Open configure.py and change the /usr/bin/env python into /usr/bin/env python3
```
$ ./configure.py
$ make -j4
$ sudo python3 setup.py install
$ sudo pip3 install .
```

## LLVM 10 + Numba Installation
```
$ wget https://github.com/llvm/llvm-project/releases/download/llvmorg-10.0.1/llvm-10.0.1.src.tar.xz
$ tar -xvf llvm-10.0.1.src.tar.xz
$ cd llvm-10.0.1.src
$ mkdir llvm_build_dir
$ cd llvm_build_dir/
$ cmake ../ -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD="ARM;X86;AArch64"
$ make -j4
$ sudo make install
$ cd bin/
$ echo "export LLVM_CONFIG=\""`pwd`"/llvm-config\"" >> ~/.bashrc
$ echo "alias llvm='"`pwd`"/llvm-lit'" >> ~/.bashrc
$ source ~/.bashrc
$ cd
$ git clone https://github.com/wjakob/tbb.git
$ cd tbb/build
$ cmake ..
$ make -j
$ sudo make install
$ pip3 install llvmlite
$ pip3 install numba
```

## PyTorch
```
https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform/index.html
```

```
https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048
```

## OpenCV + GPU

This script is based on the https://github.com/mdegans/nano_build_opencv script with minor changes.
Download `build_opencv.sh` from this repository.

CUDA_ARCH_BIN is selected as 5.3. See https://developer.nvidia.com/cuda-gpus for arch version for your device.

You may want to uninstall opencv-python first via pip using `pip3 uninstall opencv-python` or `python -m pip uninstall opencv-python`

The comiplation takes a long time and may clog up all your resources. It is better to close every other application and incerase swap file as described [here](https://www.forecr.io/blogs/programming/how-to-increase-swap-space-on-jetson-modules).

```
sudo apt-get update
sudo apt-get dist-upgrade -y --autoremove
bash build_opencv.sh 4.5.5
```

## Protobuf Installation
You may install Protobuf directly from Python using pip:
```
$ sudo pip3 install protobuf
```
If that doesn't work, you may build from source:
```
$ git clone https://github.com/protocolbuffers/protobuf.git
$ cd protobuf
$ git submodule update --init --recursive
$ ./autogen.sh
$ ./configure
$ make -j4
$ make check
$ sudo make install
$ sudo ldconfig
```

## ONNX Installation
Before proceeding to the ONNX installation, you need to perform Protobuf installation above first since ONNX heavily relies on Protobuf installation.
```
$ sudo pip3 install onnx
```

## Keras Installation
```
$ sudo apt-get install libhdf5-serial-dev hdf5-tools libhdf5-dev
$ sudo apt-get install libblas-dev liblapack-dev libatlas-base-dev gfortran
$ sudo pip3 install scipy
$ sudo pip3 install keras
```

## Tensorflow Installation
```
$ sudo apt-get install libhdf5-serial-dev hdf5-tools libhdf5-dev zlib1g-dev zip libjpeg8-dev
$ sudo pip3 install -U numpy==1.16.1 future==0.17.1 mock==3.0.5 h5py==2.9.0 keras_preprocessing==1.0.5 keras_applications==1.0.6 enum34 futures testresources setuptools protobuf
```
If you have trouble installing Protobuf, you may take a look at the Protobuf installation part above. If everything's fine, you may proceed. Now, take a look at this https://developer.download.nvidia.com/compute/redist/jp/. You may find a lot of JetPack versions and you need to choose one based on your preference. In this tutorial, I used v411 since it has compatibility with Python 2.7 and Python 3.6 installation. 
```
$ sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v411 tensorflow-gpu
```
If you run into a problem when execute the code above like this:
```
Exception:
Traceback (most recent call last):
  ...
  File "/usr/share/python-wheels/requests-2.18.4-py2.py3-none-any.whl/requests/models.py", line 935, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 404 Client Error: Not Found for url: https://developer.download.nvidia.com/compute/redist/jp/v411/grpcio/
```
then you should download the Tensorflow installation manually. Previously, when I executed this ```sudo pip3 install --pre --extra-index-url https://developer.download.nvidia.com/compute/redist/jp/v411 tensorflow-gpu```, it already collected tensorflow-gpu and showed an output like this:
```
Collecting tensorflow-gpu
  Downloading https://developer.download.nvidia.com/compute/redist/jp/v411/tensorflow-gpu/tensorflow_gpu-1.13.0rc0+nv19.2-cp36-cp36m-linux_aarch64.whl (204.6MB)
    100% |████████████████████████████████| 204.6MB 4.6kB/s
```
So, I downloaded tensorflow_gpu-1.13.0rc0+nv19.2-cp36-cp36m-linux_aarch64.whl. From the download directory, I ran this inside the terminal:
```
$ sudo pip3 install tensorflow_gpu-1.13.0rc0+nv19.2-cp36-cp36m-linux_aarch64.whl
```

## TBB issues
https://github.com/jefflgaol/Install-Packages-Jetson-ARM-Family/issues/2
Thank you for surefyyq for the solution regarding the TBB issue.
```
$ git clone https://github.com/wjakob/tbb.git
$ cd tbb/build
$ cmake ..
$ make -j
$ sudo make install
```

## LibUVC-backend installation
```
$ wget https://github.com/IntelRealSense/librealsense/raw/master/scripts/libuvc_installation.sh
$ chmod +x ./libuvc_installation.sh
$ ./libuvc_installation.sh
```


## Scikit learn issues
If when using scikit-learn as a dependency it says that the installation is incorrect, add `import sklearn` as a first import in your entry script.
