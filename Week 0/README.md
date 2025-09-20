# 🚀 Week 0 — Tool Installation 
---

> *Week 0 objective:* Prepare the dev environment for the India RISC-V Tapeout Program — install the required EDA toolchain on an Ubuntu VM and provide visual proof (terminal screenshots) for each installation.

---

## 🖥 Recommended VM / System config
- *RAM:* 6 GB  
- *Disk:* 50 GB  
- *OS:* Ubuntu 20.04 LTS or newer  
- *vCPU:* 4

If you use VirtualBox, download it here: https://www.virtualbox.org/wiki/Downloads

---

## 🧰 Tools (what & why) — short descriptions

### Yosys — RTL synthesis engine
Yosys is an open-source synthesis tool that converts your SystemVerilog/Verilog RTL into gate-level netlists. It’s a key piece of an open-source ASIC flow (used before P&R / OpenLane).
- Website / repo: https://github.com/YosysHQ/yosys

### Icarus Verilog (iverilog) — simulator
Icarus Verilog is a lightweight open-source Verilog simulator. Use it for quick functional simulation and smoke tests of your RTL and testbenches.
- Install for fast local simulation and VCD generation for GTKWave.

### GTKWave — waveform viewer
GTKWave opens .vcd / .fst waveform files produced by simulations. Use it to inspect signals, debug RTL, and capture waveform screenshots for reports.

### VirtualBox (optional) — host VM platform
Use VirtualBox to run a reproducible Ubuntu VM environment on your host machine if you prefer the sandboxed VM approach.


---

## Installation instructions (commands)

### 🔧 VirtualBox
Download and install VirtualBox from:
https://www.virtualbox.org/wiki/Downloads

---

### 🔧 Yosys Installation  
```
# Update system
sudo apt-get update  

# Clone the Yosys repository
git clone https://github.com/YosysHQ/yosys.git
cd yosys  

# Install 'make' if not already installed
sudo apt install make  

# Install required dependencies
sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev  

# Configure and build Yosys
make config-gcc
make  

# Install Yosys
sudo make install
```

*Verify Yosys installation*

```
yosys

# After this for getting Proof, type :
License
```

*Yosys installed proof:*

![Yosys installed proof](yosys_installed.jpg)

---

### 🔧 Icarus Verilog Installation  
```
# Update system
sudo apt-get update  

# Install Icarus Verilog
sudo apt-get install iverilog
```


*Verify Icarus Verilog*

```
iverilog 
```

*Icarus Verilog installed proof:*

![Icarus Verilog installed proof](iverilog_installed.jpg)

---

### 🔧 GTKWave Installation  
```
# Update system
sudo apt-get update  

# Install GTKWave
sudo apt install gtkwave
```

*Verify GTKWave*

```
gtkwave 
```

*GTKWave installed proof:*

![GTKWave installed proof](gtkwave_installed.jpg)

---
