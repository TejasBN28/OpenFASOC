# OpenFASOC
Fully Open-Source Autonomous SoC Synthesis using Customizable Cell-Based Synthesizable Analog Circuits
The FASoC Program is focused on developing a complete system-on-chip (SoC) synthesis tool from user specification to GDSII with fully open-sourced tools.

# Installation
## 1. OpenFASOC:
The command used to install OpenFASOC are 
```
git clone https://github.com/idea-fasoc/openfasoc

cd openfasoc

pip install -r requirements.txt
```
For the complete steps of installing OpenFASOC, refer Manual Installation from [here](https://github.com/idea-fasoc/OpenFASOC/blob/main/docs/source/getting-started.rst). 

## 2. OpenROAD: 
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

## 3. Klayout
Downlaod the latest version of the Klayout from [here](https://www.klayout.de/build.html). Install the following dependencies: qt5-default, qttools5-dev, libqt5xmlpatterns5-dev, qtmultimedia5-dev, libqt5multimediawidgets5 and libqt5svg5-dev.
```
sudo apt-get install -y libqt5widgets5

sudo dpkg -i klayout_0.27.11-1_amd64.deb

```

## 4. Netgen
To install Netgen, 
```
sudo add-apt-repository ppa:ngsolve/ngsolve

sudo apt-get update

sudo apt-get install ngsolve

```

## 5. Yosys
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

## 6. Magic
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

# Temperature Sensor Generator
Circuit
-------
This generator creates a compact mixed-signal temperature sensor based on the topology from this [paper](https://ieeexplore.ieee.org/document/9816083).

It consists of a ring oscillator whose frequency is controlled by the voltage drop over a MOSFET operating in subthreshold regime, where its dependency on temperature is exponential.

![tempsense_ckt](https://user-images.githubusercontent.com/110079631/199317479-67f157c5-6934-470b-8552-5451b1361b9c.png)

  Block diagram of the temperature sensor’s circuit

The physical implementation of the analog blocks in the circuit is done using two manually designed standard cells:
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


The generator uses this model file to automatically determine the number of headers and inverters, among other necessary modifications that can be made to meet spec. The generator references the model file in an iterative process until either meeting spec or failing. A verilog description is then
produced by substituting specifics into several template verilog files.

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
