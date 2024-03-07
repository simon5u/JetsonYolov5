Running YoloV5 with TensorRT Engine on Jetson Orin Nano 8GB.
==========

This repository contains step by step guide to build and convert YoloV5 model into a TensorRT engine on Jetson Orin Nano 8GB.

Please install Jetpack OS version 5.1.1 as mentioned by Nvidia and follow below steps. Please follow each steps exactly mentioned in the video links below :

Build YoloV5 TensorRT Engine on Jetson Nano: https://www.youtube.com/watch?v=ErWC3nBuV6k

Object Detection YoloV5 TensorRT Engine on Jetson Nano: https://www.youtube.com/watch?v=-Vu65N1NRWw

Jetson Xavier:

<img src="videos/out.jpg" width="800"/>

Install Libraries
=============
Please install below libraries::

    sudo apt-get update
	sudo apt-get install -y liblapack-dev libblas-dev gfortran libfreetype6-dev libopenblas-base libopenmpi-dev libjpeg-dev zlib1g-dev
	sudo apt-get install -y python3-pip
	

Install below python packages
=============
Numpy comes pre installed with Jetpack, so make sure you uninstall it first and then confirm if it's uninstalled or not. Then install below packages:

    numpy==1.19.0
	pandas==0.22.0
	Pillow==8.4.0
	PyYAML==3.12
	scipy==1.5.4
	psutil
	tqdm==4.64.1
	imutils

 You can put all these libraries into a req.txt. Then run, pip3 install -r req.txt

Install PyCuda
=============
We need to first export few paths. To install tensorrt into python3, you can refer to https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#maclearn-net-repo-install.

	export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
	export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
 	sudo apt-get install python3-libnvinfer
	python3 -m pip install pycuda --user
	

Install Seaborn
=============

    $ sudo apt install python3-seaborn
	
Install torch & torchvision
=============

	wget https://github.com/KumaTea/pytorch-aarch64/releases/download/v1.11.0/torch-1.11.0-cp38-cp38-linux_aarch64.whl
	pip3 install torch-1.11.0-cp38-cp38-linux_aarch64.whl
	git clone --branch v0.13.0 https://github.com/pytorch/vision torchvision
	sudo chmod -R 777 /usr/local/lib/python3.8/dist-packages/ 
 	cd torchvision
	python3 setup.py install 
	
### Not required but good library
python3 -m pip install -U jetson-stats==3.1.4

This marks the installation of all the required libraries.

------------------------------------------------------------------------------------------

Generate wts file from pt file
=============
Yolov5s.pt and Yolov5n.pt are already provided in the repo. But if you want you can download any other version of the yolov5 model. Then run below command to convert .pt file into .wts file 

	cd JetsonYoloV5
	python3 gen_wts.py -w yolov5m.pt -o yolov5m.wts
	
Make
=============
Create a build directory inside yolov5. Copy and paste generated wts file into build directory and run below commands. If using custom model, make sure to update kNumClas in yolov5/src/config.h

	cd yolov5/
	mkdir build
	cd build
	cp ../../yolov5m.wts .
	cmake ..
	make 
	
Build Engine file 
=============

    $ ./yolov5_det -s yolov5m.wts yolov5m.engine m
	

Testing Engine file 
=============

	$ ./yolov5_det -d yolov5m.engine ../images
	
This will do inferencing over images and output will be saved in build directory.

-----------------------------------------------------------------------------------------

Python Object Detection
=============
Use `app.py` to do inferencing on any video file or camera.

	$ python3 app.py

If you have custom model, make sure to update categories as per your classes in `yolovDet.py` .
