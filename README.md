# OpenFASOC
OpenFASOC is focused on automating analog generation using  open-source tools. OpenFASOC converts user specification to GDSII with fully open-sourced tools. This project is led by a team of researchers at the University of Michigan. Fully Open-Source Autonomous SoC Synthesis OpenFASOC uses Customizable Cell-Based approach to synthesize analog circuits. Basically, OpenFASoC Program is focused on developing a complete system-on-chip (SoC) synthesis tool from user specification to GDSII with fully open-sourced tools. 

# Table of contents
 - [1. Introduction](#1-Introduction)<br>
 	- [1.1 Traditional Analog Design Flow](#11-Traditional-Analog-Design-Flow)
 	- [1.2 OpenFASOC Analog Design Flow](#12-OpenFASOC-Analog-Design-Flow)
 - [2. Installation](#2-Installation)<br>
 	- [2.1 OpenFASOC](#21-OpenFASOC)
 	- [2.2 OpenROAD](#22-OpenROAD)
 	- [2.3 Klayout](#23-Klayout)
 	- [2.4 Netgen](#24-Netgen)
 	- [2.5 Yosys](#25-Yosys)
 	- [2.6 Magic](#26-Magic)
- [3. Temperature Sensor Generator](#3-Temperature-Sensor-Generator)

# 1. Introduction
OpenFASOC is the world's first autonomous mixed-signal SoC framework, driven entirely by user constraints, along with a suite of automated generators for analog blocks. The process-agnostic framework takes high-level user intent as inputs to generate optimized and fully verified analog blocks using a cell-based design methodology. <br>
## 1.1 Traditional Analog Design Flow
The traditional analog design flow is shown in the below block diagram. Here, the first step is to draw a schematic of the analog circuit to satisfy the user specifications. Then, the schematic is simulated to check if it meets the required specifications. If the specs are satisfied, then the layout of the schematic is drawn manually. Till this step, everything is manually generated. Foe the drawn layout, Design Rule Checks (DRC), Layout Vs Schematic (LVS) and Parasitic Extraction (PEX) is automated.<br><br>
<p align="center">
<img src="https://user-images.githubusercontent.com/110079788/207111619-282f01ac-76b6-409c-abca-30338ddb5ef4.png">
</p>

## 1.2 OpenFASOC Analog Design Flow
The main drawback of Traditional Analog Design Flow is that the design time is very huge (approximately 5 to 6 months). The main objective of openfacsoc is to reduce the design time from 5 to 6 months to a few hours by leveraging digital design eda tools to automate analog design flow.
<p align="center">
<img src="https://user-images.githubusercontent.com/110079788/207117484-d3c37426-f8f0-42c5-b286-f4529392e96f.png">
</p>
The OpenFASOC Design flow starts by taking the design specifications in the form of `.json` format. Then the OpenFASOC generator determines the number of auxillary cell to be added to optimize the design. The generator uses the model file model file to automatically determine the number of Aux-Cell to be added. Here, the model file is saved in the form of a `.csv` file. <br><br>In other words, the generator iteratively searches the the model file to find the optimum number of auxillary cells to be added. After finding the optimum structure, the behavioral verilog description is created. Then the synthesis tool `yosys` maps the behavioral verilog to the structural verilog. This step is followed by place and route step. Place and Route is performed by OpenROAD tool. This step is followed by Design Rule Checks (DRC), Layout Vs Schematic (LVS) and Parasitic Extraction (PEX). Unlike traditional Analog-Design flow, in OpenFASOC flow, the complete process of aux-cell generation to layout generation is automated which significantly reduced the design time. 

<p align="center">
<img src="https://user-images.githubusercontent.com/110079788/207131158-2a54f948-9016-43ca-9977-772771e89791.png">
</p>

### Auxillary Cell based Approach:
The idea behind OpenFASOC tool is the use of auxillary cells. Aux-cells are small analog cells with specific analog functionality used by analog blocks. They are derived from a template and characterized/optimized per PDK. They are usually formed of a maximum of 12 transitors. The layout of the auxillary cells is automatically generated using Align tool. 
<br><br>The idea of using these Aux-Cells is that by using variable number of them, the output characteristics can be altered. A good example to understand the auxillary cells and their impact on the design is Digitally Controlled Oscillator. In the DCO example, there are two Auxillary cells:
 - Differential Tri-State Buffer
 - Capacitor

In DCO, a ring oscillator like structure is created. By varying the number of buffers/inverters connected in parallel and the size of the capactors, we can vary the charging time of the capacitor thereby varying the frequency of the DCO.

<p align="center">
<img src="https://user-images.githubusercontent.com/110079788/207123929-229a9bf0-3174-43c9-9510-0cd015101a17.png">
</p>


# 2. Installation
## 2.1 OpenFASOC:
The command used to install OpenFASOC are 
```
git clone https://github.com/idea-fasoc/openfasoc
cd openfasoc
pip install -r requirements.txt
```
For the complete steps of installing OpenFASOC, refer Manual Installation from [here](https://github.com/idea-fasoc/OpenFASOC/blob/main/docs/source/getting-started.rst). 

## 2.2 OpenROAD: 
OpenROAD is an integrated chip physical design tool that takes a design from synthesized Verilog to routed layout. OpenROAD uses the OpenDB database and OpenSTA for static timing analysis. Documentation is also available [here](https://openroad.readthedocs.io/en/latest/main/README.html).


The commands to install OpenROAD are,
```
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD.git
cd OpenROAD
./etc/DependencyInstaller.sh
./etc/DependencyInstaller.sh -run
./etc/DependencyInstaller.sh -dev
mkdir build
cd build
cmake ..
make
```

## 2.3 Klayout
Downlaod the latest version of the Klayout from [here](https://www.klayout.de/build.html). Install the following dependencies: qt5-default, qttools5-dev, libqt5xmlpatterns5-dev, qtmultimedia5-dev, libqt5multimediawidgets5 and libqt5svg5-dev.
```
sudo apt-get install -y libqt5widgets5
sudo dpkg -i klayout_0.27.11-1_amd64.deb
```

## 2.4 Netgen
To install Netgen, 
```
sudo add-apt-repository ppa:ngsolve/ngsolve
sudo apt-get update
sudo apt-get install ngsolve
```

## 2.5 Yosys
The software used to run gate level synthesis is Yosys. Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains. Yosys can be adapted to perform any synthesis job by combining the existing passes (algorithms) using synthesis scripts and adding additional passes as needed by extending the Yosys C++ code base.


To install yosys, install the prerequisites using the following command 
 ```
 sudo apt-get install build-essential clang bison flex \
	libreadline-dev gawk tcl-dev libffi-dev git \
	graphviz xdot pkg-config python3 libboost-system-dev \
	libboost-python-dev libboost-filesystem-dev zlib1g-dev
```
To install latest Version of Yosys, 
```
git clone https://github.com/YosysHQ/yosys.git
make
sudo make install 
make test
```

## 2.6. Magic
Run following commands one by one to fulfill the system requirement.
#### Prerequisites for magic
```
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
```
#### Install magic
```
git clone https://github.com/RTimothyEdwards/magic
cd magic/
./configure
sudo make
sudo make install
```
type `magic` terminal to check whether it installed succesfully or not. Type `exit` to exit magic.

# 3. Temperature Sensor Generator
## 3.1 Temperature Sensor Circuit
An all-digital temperature sensor, that relies on a new subthreshold oscillator (achieved using the auxiliary cell “Header Cell“) for realizing synthesizable thermal sensors. The way that works is we have a subthreshold current that has an exponential dependency on the temperature, the frequency generated from the subthreshold ring oscillator is also dependent on temperature. So we can sense the temperature by comparing the difference between the clock frequency generated from a reference oscillator and the clock frequency from the proposed frequency generator. Block diagram of the temperature sensor’s circuit is shown below.

<p align="center">
  <img src="https://user-images.githubusercontent.com/110079631/199317479-67f157c5-6934-470b-8552-5451b1361b9c.png">
</p><br>

The physical implementation of the analog blocks in the circuit is done using two manually designed standard cells (auxillary cells):
1. HEADER cell, containing the transistors in subthreshold operation;
2. SLC cell, containing the Split-Control Level Converter.

The gds and lef files of HEADER and SLC cells are pre-created before the start of the Generator flow.
The layout of the HEADER cell is shown below:

<p align="center">
  <img src="/images/of1.png">
</p><br>

The layout of the SLC cell is shown below:

<p align="center">
  <img src="/images/of2.png">
</p><br>

# OpenFASOC Flow
<p align="center">
  <img src="/images/of3.png">
</p><br>

The generator must first parse the user’s requirements into a high-level circuit description or verilog. User input parsing is implemented by reading from a JSON spec file directly in the temp-sense-gen repository. The JSON allows for specifying power, area, maximum error (temperature result accuracy),
an optimization option (to choose which option to prioritize), and an operating temperature range (minimum and maximum operating temperature values).
The operating temperature range and optimization must be specified, but other items can be left blank. 

The generator uses this model file to automatically determine the number of headers and slc, among other necessary modifications that can be made to meet spec. The generator references the model file in an iterative process until either meeting spec or failing. A verilog description is then
produced by substituting specifics into template verilog files.

## Case Study: Temp_Sensor

### 1. Verilog Files geneartion and dir? User specs, iterative approach and generated verilog files
The test.json file shown in the below screenshot corresponds to the temp_sense_gen.

<p align="center">
  <img src="/images/of4.png">
</p><br>

In the test.json file, only the temperature can be modified and the range of temperature should always be between –20C to 100C. Based on the operating temperature range, generator calculates the number of header and inverters to minimize the error. 

To run the verilog generation, run the following command:
```
make sky130hd_temp_verilog
```

The generator references the model file in an iterative process until either meeting specifications or failing.

<p align="center">
  <img src="/images/of5.png">
</p><br>

As shown in the above picture, the tool is trying to minimize the error iteratively, by varying the number of inverters and headers for the given temperature range.

#### Results 
For temperature Range –20C to 100C, the error and inverters and headers are given below:
<p align="center">
  <img src="/images/of6.png">
</p><br>

For temperature Range 30C to 100C, the error and inverters and headers are given below:
<p align="center">
  <img src="/images/of7.png">
</p><br>

#### Directories where the resulting verilog is Created:
The screenshot of the files created before and after is given below:
#### After
<p align="center">
  <img src="/images/of8.png">
</p><br>

#### Before
<p align="center">
  <img src="/images/of9.png">
</p><br>

Here, using the generic template, extra blocks of counter, TEMP_ANALOG_hv.nl.v, TEMP_ANALOG_lv.nl.v
are created in the src folder.
