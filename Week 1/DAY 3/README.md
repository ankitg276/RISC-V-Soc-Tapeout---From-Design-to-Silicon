
# 🚀 DAY 3: Combinational and Sequential Logic Optimizations

Welcome to an exciting journey into the world of digital design optimization! This repository serves as a dynamic lab guide for mastering **combinational** and **sequential logic optimizations** using **Yosys**, an open-source synthesis tool. Our goal is to transform complex logic circuits into sleek, efficient designs that minimize **area**, **power**, and **timing delays** while preserving functionality. Perfect for VLSI enthusiasts, this guide blends theory with hands-on labs, complete with Verilog synthesis, visualizations, and practical insights.

---

📚 **Table of Contents**  
🔹 [Combinational Logic Optimization](#combinational-logic-optimization)  
🔹 [Sequential Logic Optimization](#sequential-logic-optimization)  
🔹 [Combinational Logic Optimization Labs](#combinational-logic-optimization-labs)  
🔹 [Sequential Logic Optimization Labs](#sequential-logic-optimization-labs)
🔹 [Summary](#summary)  
🔹 [Key Takeaways](#key-takeaways)
---

## 🔧 Combinational Logic Optimization

### 🎯 Objective
The mission is to streamline combinational logic circuits, slashing gate counts and power usage while keeping functionality intact. By eliminating redundancies and simplifying expressions, we create compact designs ideal for high-density integrated circuits, balancing **cost** and **performance**.

### 🛠️ Techniques

#### ➡️ Constant Propagation
This technique swaps out logic blocks with fixed constants when inputs are known, cutting unnecessary computations.

**Example:**  
For the expression:  
`Y = ((A · B) + C)'`  
If `A = 0`:  
`Y = ((0 · B) + C)' = C'`  
- **Before:** ~6 MOS transistors (AND, OR, NOT gates).  
- **After:** A single inverter (~2 MOS transistors).  
💡 **Impact:** Shrinks area and reduces power consumption.

#### ➡️ Boolean Logic Optimization
Simplify Boolean expressions to their leanest form using:  
- **Karnaugh Map (K-Map):** A visual tool to group minterms for minimal SOP/POS expressions (great for ≤6 variables).  
- **Quine-McCluskey Method:** An algorithmic approach for larger logic functions, automating minimal expression derivation.

**Verilog Example:**  
Original:  
`assign y = a ? (b ? c : (c ? a : 0)) : (!c);`  
Optimized:  
`y = a ⊕ c` (single XOR gate)  
💡 **Impact:** Reduces gate depth, boosting speed.

**🛠️ Yosys Tips:**  
- Use `opt_clean` to remove unused wires and cells.  
- For hierarchical designs, run `flatten` before optimization to unlock global simplifications.  
- **Command:** After `synth -top <module_name>`:  
  ```
  opt_clean -purge
  ```  
  The `-purge` flag clears internal nets, even those with public names, for a pristine netlist.

---

## ⏳ Sequential Logic Optimization

### 🎯 Basic Techniques

#### ➡️ Sequential Constant Propagation
Extends constant propagation to flip-flops, simplifying or eliminating registers by propagating known values during synthesis.

### 🎯 Advanced Techniques

#### ➡️ State Optimization
Minimizes FSM states via merging and optimal encoding (e.g., one-hot, binary, gray), reducing area and transition overhead.

#### ➡️ Retiming
Repositions registers to balance path delays, shortening critical paths and boosting clock frequency.

#### ➡️ Sequential Logic Cloning
Duplicates logic in floorplan-aware synthesis to ease timing constraints and reduce routing congestion.

---

## 🔬 Combinational Logic Optimization Labs

Dive into hands-on labs to apply combinational optimizations using Yosys. Replace placeholder image paths with actual visuals for netlists and waveforms.

### 🧪 Lab 1: Basic Constant Propagation
**Goal:** Simplify logic with constant inputs.  
**Files:** `opt_check.v`  
**Outcome:** Reduced gate count via constant propagation.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v 
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 1 Netlist](path/to/lab1_opt_check.png)

### 🧪 Lab 2: Redundant Logic Removal
**Goal:** Eliminate duplicate logic.  
**Files:** `opt_check2.v`

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v 
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 2 Netlist](path/to/lab2_opt_check2.png)

### 🧪 Lab 3: Boolean Simplification
**Goal:** Apply K-Map simplifications.  
**Files:** `opt_check3.v`

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v 
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 3 Netlist](path/to/lab3_opt_check3.png)

### 🧪 Lab 4: Advanced Expression Reduction
**Goal:** Condense nested logic to primitive gates.  
**Files:** `opt_check4.v`

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check4.v 
synth -top opt_check4
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 4 Netlist](path/to/lab4_opt_check4.png)

### 🧪 Lab 5: Hierarchical Design Optimization
**Goal:** Optimize a multi-module design.  
**Files:** `multiple_module_opt.v`, `multiple_module_opt_flat.v`  
**Outcome:** One AND2 gate (`a & 1`) and one A21OI gate (`(a & b) | c`).

**Phase 1: Flatten Hierarchy**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
write_verilog -noattr multiple_module_opt_flat.v
```

**Phase 2: Optimize Flattened Netlist**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt_flat.v
synth -top multiple_module_opt
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 5 Netlist](path/to/lab5_multiple_module_opt1.png)

### 🧪 Lab 6: Advanced Hierarchical Optimization
**Goal:** Global optimization of complex hierarchies.  
**Files:** `multiple_module_opt2.v`, `multiple_module_opt2_flat.v`

**Phase 1: Flatten Hierarchy**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt2.v
synth -top multiple_module_opt2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
write_verilog -noattr multiple_module_opt2_flat.v
```

**Phase 2: Optimize Flattened Netlist**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_module_opt2_flat.v
synth -top multiple_module_opt2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 6 Netlist](path/to/lab6_multiple_module_opt2.png)

---

## ⏰ Sequential Logic Optimization Labs

These labs explore sequential optimizations, focusing on flip-flop inference and unused element pruning, with waveforms and netlists for clarity.

### 🧪 Lab 1: Basic DFF with Reset
**Goal:** Observe standard DFF inference.  
**Files:** `dff_const1.v`  
**Behavior:** Output `q` updates to 1'b0 at the next clock edge after reset, retaining the DFF.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 1 Netlist](path/to/lab1_dff_const1.png)

### 🧪 Lab 2: Constant Output DFF
**Goal:** Remove DFF due to constant output.  
**Files:** `dff_const2.v`  
**Behavior:** Output `q=1'b1` regardless of reset, simplifying to constant logic.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 2 Netlist](path/to/lab2_dff_const2.png)

### 🧪 Lab 3: Back-to-Back DFFs
**Goal:** Retain DFFs due to dependency.  
**Files:** `dff_const3.v`  
**Behavior:** Outputs `q` and `q1` form a set-reset chain, preserving both DFFs.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
synth -top dff_const3
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 3 Netlist](path/to/lab3_dff_const3.png)

### 🧪 Lab 4: Independent Constants
**Goal:** Eliminate DFFs due to constant outputs.  
**Files:** `dff_const4.v`  
**Behavior:** Outputs `q1` and `q` are reset-independent, reducing to constant logic.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const4.v
synth -top dff_const4
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 4 Netlist](path/to/lab4_dff_const4.png)

### 🧪 Lab 5: Dependent Constants
**Goal:** Retain DFFs due to sequential dependency.  
**Files:** `dff_const5.v`  
**Behavior:** `q1=1'b1` after reset, `q` samples `q1`, requiring two DFFs.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const5.v
synth -top dff_const5
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 5 Netlist](path/to/lab5_dff_const5.png)

### 🧪 Lab 6: Unused Outputs in Multi-Bit Register
**Goal:** Optimize a 3-bit counter using only the LSB.  
**Files:** `counter_opt.v`  
**Behavior:** Only `count[0]` drives `q`, reducing to a single flip-flop.

**Synthesis Without `opt_clean`:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 6 Netlist (No Opt)](path/to/lab6_counter_opt_no_opt.png)

**Synthesis With `opt_clean`:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 6 Netlist](path/to/lab6_counter_opt.png)

### 🧪 Lab 7: Full Counter Utilization
**Goal:** Retain all counter bits for output.  
**Files:** `counter_opt2.v`  
**Behavior:** All `count[2:0]` bits generate `q` (set at `3'b100`), retaining 3 DFFs.

**Synthesis Script:**
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt2.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Lab 7 Netlist](path/to/lab7_counter_opt2.png)

---

## 📝 Summary
This repository provides a comprehensive exploration of logic optimization techniques for digital circuit design, focusing on both combinational and sequential circuits. Through theoretical explanations and practical labs, you'll learn to use Yosys to simplify logic, reduce gate counts, and optimize timing and power. The labs cover real-world scenarios, from constant propagation to hierarchical design flattening, and demonstrate how to eliminate redundant logic and streamline sequential elements like flip-flops. By the end, you'll have hands-on experience with synthesizing Verilog designs, visualizing netlists, and applying optimization techniques critical for efficient VLSI design.

---

## 🌟 Key Takeaways
- **Combinational Optimization:** Master techniques like constant propagation and Boolean simplification to reduce gate count and power, using tools like K-Maps and Yosys's `opt_clean` command.
- **Sequential Optimization:** Understand how to propagate constants through flip-flops, minimize FSM states, retime registers, and clone logic for timing and congestion benefits.
- **Hierarchical Designs:** Learn the importance of flattening multi-module designs before optimization to enable global simplifications.
- **Practical Application:** Gain proficiency in Yosys synthesis flows, including commands like `synth`, `flatten`, `opt_clean -purge`, and `abc` for mapping to standard cells.
- **Real-World Impact:** Optimize designs for area, power, and performance, crucial for modern integrated circuit design.

---