
# 🌟DAY 4 - Mastering GLS, Blocking vs. Non-Blocking, and Synthesis-Simulation Mismatches

Dive into the fascinating world of digital design verification with this vibrant lab guide! Designed for VLSI enthusiasts and Verilog learners, this repository explores **Gate-Level Simulation (GLS)**, the critical differences between **blocking** and **non-blocking assignments**, and the art of diagnosing **synthesis-simulation mismatches**. Using **Yosys** for synthesis and **Icarus Verilog (iverilog)** for simulation, you’ll gain hands-on experience through engaging labs that illuminate common pitfalls and best practices. Get ready to synthesize, simulate, and perfect your designs!

---

## 🚀 What's Inside?

- **🔍 Introduction**: Understand GLS and why it’s essential for post-synthesis verification.
- **⚡ Key Concepts**: Explore blocking vs. non-blocking assignments and synthesis-simulation mismatches.
- **🧪 Lab Experiments**:
  - Synthesis and GLS of a ternary operator MUX.
  - Analysis of a flawed MUX design with sensitivity list issues.
  - Investigation of blocking assignment pitfalls in the `blocking_caveat` design.
- **🎯 Learning Outcomes**: Master Verilog coding, GLS workflows, and mismatch resolution.

---

## 📝 Introduction

This guide is your ticket to mastering **Gate-Level Simulation (GLS)**, a crucial step in verifying synthesized digital designs. GLS tests the **post-synthesis netlist**—the gate-level representation of your RTL code—ensuring it matches the intended functionality. You’ll also uncover why **synthesis-simulation mismatches** occur, often due to coding errors like incomplete sensitivity lists or misuse of blocking assignments. Through practical labs, you’ll synthesize Verilog designs, run GLS, and learn to spot and fix discrepancies, ensuring robust ASIC and FPGA designs.

---

## ⚡ Key Concepts

### 🛠️ Gate-Level Simulation (GLS)
GLS verifies the synthesized netlist using the same testbench as the RTL simulation, ensuring logical equivalence and, optionally, timing accuracy.

- **RTL Simulation**: Tests the Verilog RTL code (pre-synthesis).
- **GLS**: Tests the gate-level netlist, incorporating standard cell models (e.g., Sky130).
- **Purpose**: Catches mismatches between RTL and synthesized designs, ensuring functional correctness.

**Delay Modes**:
- **Zero Delay (Functional)**: Verifies logic without timing, similar to RTL simulation.
- **Full Timing**: Includes propagation delays for both functional and timing validation.

**GLS Flow with Icarus Verilog**:
![GLS Workflow](path/to/gls_workflow.png)
```
iverilog <path-to-gate-level-models> <netlist.v> <testbench.v>
```

### ⚠️ Synthesis-Simulation Mismatch
Mismatches occur when RTL and GLS produce different results. Common culprits:
1. **Incomplete Sensitivity Lists**: Omitting signals in `always` blocks (e.g., `@(sel)` instead of `@(sel, i0, i1)`), causing simulation to miss input changes.
2. **Blocking vs. Non-Blocking Assignments**:
   - **Blocking (`=`)**: Updates variables sequentially in simulation, mimicking procedural code.
   - **Non-Blocking (`<=`)**: Evaluates RHS, assigns to LHS at the end of the simulation tick, reflecting hardware parallelism.
   - **Synthesis Issue**: Synthesis treats both as non-blocking, potentially causing mismatches if RTL relies on blocking behavior.
3. **Non-Standard Coding**: Poor practices, like latches from incomplete sensitivity lists.

**Example**:
![Sensitivity List Issue](path/to/sensitivity_issue.png)
- **Incorrect**: `always @(sel)` misses `i0`, `i1` changes, acting like a latch.
- **Correct**: `always @(sel, i0, i1)` ensures proper MUX behavior.

---

## 🧪 Lab Experiments

### 🔬 Lab 1: Synthesizing a Ternary Operator MUX
**Objective**: Synthesize a well-coded MUX using a ternary operator.  
**Files**: `ternary_operator_mux.v`, `tb_ternary_operator_mux.v`  
**Description**: Implements a MUX with `assign y = sel ? i1 : i0`, ensuring synthesis-friendly design.

#### RTL Simulation
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![RTL Waveform](path/to/ternary_mux_rtl.png)

#### Synthesis
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr ternary_operator_mux_net.v
```
![Netlist](path/to/ternary_mux_netlist.png)

**Result**: Produces a clean MUX2 gate, free of mismatches.

---

### 🔬 Lab 2: GLS of Ternary Operator MUX
**Objective**: Verify the synthesized MUX netlist.  
**Files**: `ternary_operator_mux_net.v`, `tb_ternary_operator_mux.v`

```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![GLS Waveform](path/to/ternary_mux_gls.png)

**Result**: GLS matches RTL, confirming functional equivalence.

---

### 🔬 Lab 3: Synthesizing a Flawed MUX
**Objective**: Synthesize a MUX with an incomplete sensitivity list.  
**Files**: `bad_mux.v`, `tb_bad_mux.v`  
**Description**: Uses `always @(sel)`, ignoring `i0`, `i1`, leading to latch-like behavior in simulation.

#### RTL Simulation
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![RTL Waveform](path/to/bad_mux_rtl.png)

#### Synthesis
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr bad_mux_net.v
```
![Netlist](path/to/bad_mux_netlist.png)

**Result**: Synthesis produces a correct MUX, ignoring the sensitivity list.

---

### 🔬 Lab 4: GLS of Flawed MUX
**Objective**: Run GLS to compare with RTL.  
**Files**: `bad_mux_net.v`, `tb_bad_mux.v`

```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![GLS Waveform](path/to/bad_mux_gls.png)

**Result**: GLS shows correct MUX behavior, unlike RTL’s latch-like output.

---

### 🔬 Lab 5: Mismatch Analysis for Flawed MUX
**Objective**: Diagnose the synthesis-simulation mismatch.  
**Description**: The RTL’s incomplete sensitivity list causes latch behavior, while synthesis produces a proper MUX.

**Comparison**:
![Mismatch Waveform](path/to/bad_mux_mismatch.png)
- **RTL**: `y` updates only on `sel` changes.
- **GLS**: `y` tracks `sel`, `i0`, `i1` correctly.

**Fix**: Use `always @(sel, i0, i1)`.

---

### 🔬 Lab 6: Synthesizing Blocking Caveat Design
**Objective**: Synthesize a design with blocking assignments.  
**Files**: `blocking_caveat.v`, `tb_blocking_caveat.v`  
**Description**: Blocking assignments (`=`) in an `always` block cause sequential updates in simulation.

#### RTL Simulation
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![RTL Waveform](path/to/blocking_caveat_rtl.png)

#### Synthesis
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr blocking_caveat_net.v
```
![Netlist](path/to/blocking_caveat_netlist.png)

**Result**: Synthesis assumes non-blocking behavior, producing parallel updates.

---

### 🔬 Lab 7: GLS of Blocking Caveat Design
**Objective**: Verify the synthesized netlist.  
**Files**: `blocking_caveat_net.v`, `tb_blocking_caveat.v`

```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![GLS Waveform](path/to/blocking_caveat_gls.png)

**Result**: GLS shows parallel updates, differing from RTL.

---

### 🔬 Lab 8: Mismatch Analysis for Blocking Caveat
**Objective**: Analyze the mismatch due to blocking assignments.  
**Description**: RTL’s sequential updates contrast with synthesis’s parallel updates.

**Comparison**:
![Mismatch Waveform](path/to/blocking_caveat_mismatch.png)
- **RTL**: Sequential updates from blocking assignments.
- **GLS**: Simultaneous updates from synthesized hardware.

**Fix**: Use non-blocking assignments (`<=`).

---

## 🎯 Learning Outcomes
- **GLS Mastery**: Perform GLS to validate post-synthesis designs.
- **Coding Best Practices**: Avoid incomplete sensitivity lists and blocking assignments in sequential logic.
- **Mismatch Resolution**: Diagnose and fix synthesis-simulation mismatches.
- **Tool Proficiency**: Use Yosys and Icarus Verilog effectively.

---