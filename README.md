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
