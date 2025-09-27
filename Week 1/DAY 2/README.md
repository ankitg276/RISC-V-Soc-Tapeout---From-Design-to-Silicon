# DAY 2 — Timing Libraries, Synthesis Approaches, & Efficient Flip-Flop Coding 🛠️⚡

![DAY 2 Banner](figures/banner_day2.png)

**Welcome to Day 2 of the RTL Workshop.**
This README is a **merged, comprehensive** document combining your original lecture-style notes, the compact cheat-sheet version, and a repo-ready README. It contains every major bit of content you supplied: Liberty/.lib explanation (SKY130 example), standard-cell comparison, hierarchical vs flat synthesis (Yosys), full flip-flop RTL examples (async/sync/hybrid) and simulation/synthesis workflows, interesting synthesis optimisations (`mul2`, `mult8`), recommended repo layout, figure placeholders, quick tips and gotchas.

---

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](#license) [![Tool: Yosys](https://img.shields.io/badge/Tool-Yosys-orange.svg)](#quick-start--tips) [![PDK: SKY130](https://img.shields.io/badge/PDK-SKY130-green.svg)](#timing-libraries-sky130-lib)

---

## Table of contents

* [Overview](#overview)
* [Timing Libraries — SKY130 `.lib`](#timing-libraries--sky130-lib)

  * [SKY130 PDK overview](#sky130-pdk-overview)
  * [Decoding `tt_025C_1v80`](#decoding-tt_025c_1v80)
  * [Opening & exploring the `.lib` file](#opening--exploring-the-lib-file)
  * [Quick checklist & troubleshooting](#quick-checklist--troubleshooting)
* [Standard Cell Examples & Comparison](#standard-cell-examples--comparison)

  * `a2111o_1` spotlight
  * `and2_1`, `and2_2`, `and2_4` comparison
* [Hierarchical vs Flat Synthesis (Yosys)](#hierarchical-vs-flat-synthesis-yosys)

  * Example flows (hierarchical & flattened)
  * Comparison table & visuals
  * When to use which
* [Why Use Flops? (Avoiding Glitches)](#why-use-flops-avoiding-glitches)
* [Flip-Flop Coding Styles (Full RTL source)](#flip-flop-coding-styles-full-rtl-source)

  * Async reset DFF — full file
  * Async set DFF — full file
  * Sync reset DFF — full file
  * Async + Sync hybrid DFF — full file
* [Simulation & Synthesis Workflow](#simulation--synthesis-workflow)

  * Icarus Verilog simulation (iverilog + gtkwave)
  * Yosys synthesis (read_liberty, dfflibmap, abc)
* [Interesting Optimisations (`mul2`, `mult8`)](#interesting-optimisations-mul2-mult8)

  * Example RTL (full)
  * What synthesis does
* [Repository layout & figure placeholders](#repository-layout--figure-placeholders)
* [Quick start & tips / Gotchas](#quick-start--tips--gotchas)
* [Files in this directory (recommended)](#files-in-this-directory-recommended)
* [Key takeaways](#key-takeaways)
* [License](#license)
* [Next steps I can do for you](#next-steps-i-can-do-for-you)

---

## Overview

This file blends lecture-style explanations and a compact cheat-sheet into a single README suitable for GitHub. It is intended for hands-on workshops and for being dropped into a repo alongside RTL and figure files. Everything you previously provided is preserved and reorganized for clarity and usability.

---

## Timing Libraries — SKY130 `.lib`

### SKY130 PDK overview

The SKY130 PDK is an open-source Process Design Kit based on SkyWater Technology's 130 nm CMOS process. It supplies foundry models, standard cell libraries, LEF/DEF, and Liberty (`.lib`) timing files used by synthesis and STA tools.

### Decoding `tt_025C_1v80`

* `tt` — typical process corner
* `025C` — 25°C temperature (affects timing)
* `1v80` — core voltage = 1.8 V

This naming shows the Process-Voltage-Temperature (PVT) corner the library models.

### Opening & exploring the `.lib` file

`.lib` is an ASCII format — open with any text editor. Example:

```bash
sudo apt install gedit         # or use your favorite editor
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```

Structure:

1. **Library header:** library name, technology, units (time, power, voltage, current, capacitance), operating conditions.
2. **Cell blocks:** `cell (name) { ... }` that contain pin definitions, timing arcs (lookup tables), pin capacitances, area and optional attributes.

> **Figure placeholder:** `figures/liberty_snapshot.png` — header + a representative cell block.

### Quick checklist & troubleshooting

* Confirm you are using the correct `.lib` corner for synthesis (tt/min/max as required).
* Pass the same `.lib` file to `read_liberty` and `abc -liberty` to avoid mis-mapping.
* If timing or mapping looks odd, inspect per-cell timing arcs in the `.lib`.

---

## Standard Cell Examples & Comparison

### Standard Cell spotlight — `a2111o_1`

* **Function:** `X = ((A1 & A2) | B1 | C1 | D1)`
* Useful where an AND term plus a number of OR terms reduces logic or makes gating simpler.

> **Figure placeholder:** `figures/a2111o_block.png` — gate/block diagram + timing arcs.

### Standard cell comparison — `and2_1`, `and2_2`, `and2_4`

* Identical logic; different drive strengths and transistor sizing.
* `and2_1` — weakest drive, smallest area/power.
* `and2_2` — medium.
* `and2_4` — strongest drive, largest area.

> **Figure placeholder:** `figures/and2_comparison.png` — delay vs area vs drive-strength plot.

**Rule of thumb:** choose the smallest cell that meets the timing slack; over-driving wastes area and increases power.

---

## Hierarchical vs Flat Synthesis (Yosys)

### Concepts

* **Hierarchical:** preserves RTL module boundaries; synthesis and mapping maintain submodules. Good for large designs, incremental build, and debugging.
* **Flat:** collapses module hierarchy into a single-level netlist, enabling whole-design optimizations but may increase resource usage and complicate debugging.

### Hierarchical Yosys flow (example)

```sh
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show multiple_modules
write_verilog -noattr multiple_modules.v
```

* Preserves module hierarchy (`multiple_modules` top; `sub_module1`, `sub_module2` remain).

### Flattened flow (example)

```sh
# after reading & synth
flatten
write_verilog -noattr multiple_modules_flat.v
```

* No submodules preserved — hierarchy collapsed.

> **Figure placeholders:**
> `figures/hier_flat.png`, `figures/multiple_modules_heir.png`, `figures/multiple_modules_flat.png`

### Comparison table

| Aspect             |                     Hierarchical |                          Flattened |
| ------------------ | -------------------------------: | ---------------------------------: |
| Hierarchy          |                        Preserved |                          Collapsed |
| Optimization scope |                     Module-level |                       Whole-design |
| Runtime            |         Faster for large designs |           Slower for large designs |
| Debugging          |       Easier (trace back to RTL) |                             Harder |
| Output complexity  |                          Modular |   Single, potentially huge netlist |
| Use case           | Modular IP, analysis, easy debug | Max optimisation for small designs |

### When to use which

* **Hierarchical**: large designs, IP reuse, staged flows, easier memory usage and debugging.
* **Flat**: small designs or last-step whole-design optimisations where cross-module simplification gives big benefits.

---

## Why Use Flops? (Avoiding Glitches)

Combinational circuits can produce glitches — short pulses due to asynchronous arrival of signals. Flops (DFFs) are edge-triggered storage elements that capture stable values only at clock edges, preventing transient glitches from propagating across pipeline boundaries.

**Benefits**

* Glitch suppression
* Well-defined state boundaries and timing points (setup/hold)
* Easier pipelining and timing closure

> **Figure placeholder:** `figures/glitch_example.png` — illustrate a glitch in comb logic vs. flop-captured stable data.

---

## Flip-Flop Coding Styles — Full RTL Source (ready-to-use)

> These are **complete** RTL modules. Place them in `rtl/` and simulate/synthesize as needed.

### 1) Asynchronous Reset DFF (active-high)

```verilog
// rtl/asyncres_dff.v
module dff_asyncres (
    input  wire clk,
    input  wire async_reset, // active-high
    input  wire d,
    output reg  q
);
    always @(posedge clk or posedge async_reset) begin
        if (async_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

**Behavior:** `async_reset` has higher priority than clock — immediately sets `q = 0`.

---

### 2) Asynchronous Set DFF (active-high)

```verilog
// rtl/asyncset_dff.v
module dff_async_set (
    input  wire clk,
    input  wire async_set, // active-high
    input  wire d,
    output reg  q
);
    always @(posedge clk or posedge async_set) begin
        if (async_set)
            q <= 1'b1;
        else
            q <= d;
    end
endmodule
```

**Behavior:** `async_set` immediately sets `q = 1`.

---

### 3) Synchronous Reset DFF (active-high)

```verilog
// rtl/dff_syncres.v
module dff_syncres (
    input  wire clk,
    input  wire sync_reset, // active-high
    input  wire d,
    output reg  q
);
    always @(posedge clk) begin
        if (sync_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

**Behavior:** Reset takes effect only at clock edge.

---

### 4) Async + Sync Reset DFF (priority: async > sync)

```verilog
// rtl/asyncres_syncres.v
module dff_asyncres_syncres (
    input  wire clk,
    input  wire async_reset, // active-high
    input  wire sync_reset,  // active-high
    input  wire d,
    output reg  q
);
    always @(posedge clk or posedge async_reset) begin
        if (async_reset)
            q <= 1'b0;
        else if (sync_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

**Behavior:** Async reset overrides; when inactive, sync reset evaluated at the clock edge.

---

## Simulation & Synthesis Workflow

### Icarus Verilog (simulation)

Example for the async reset DFF:

```bash
# Compile (assuming testbench tb_dff_asyncres.v writes a VCD)
iverilog dff_asyncres_tb.vvp rtl/asyncres_dff.v tb_dff_asyncres.v

# Run
./a.out

# View waveform (if your TB created tb_dff_asyncres.vcd)
gtkwave tb_dff_asyncres.vcd
```

> **Figure placeholders:** `figures/ss-dff_async.png`, `figures/ss-dff_syncres.png`

### Yosys (synthesis & mapping)

**General flow (example):**

```
# start yosys or run as a script
yosys  "
  read_liberty -lib /path/to/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  read_verilog rtl/asyncres_dff.v
  synth -top dff_asyncres
  dfflibmap -liberty /path/to/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  abc -liberty /path/to/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show -format svg -prefix out/dff_asyncres_
  write_verilog -noattr out/dff_asyncres_net.v
"
```

**Important commands**

* `read_liberty -lib <path>` — load library for mapping.
* `read_verilog <file.v>` — load RTL.
* `synth -top <module>` — run synthesis on top module.
* `dfflibmap -liberty <path>` — maps Yosys-inferred flops to tech flops.
* `abc -liberty <path>` — technology mapping & logic optimization using the .lib.
* `show` — generate graphical netlist (needs graphviz).

---

## Interesting Optimisations — `mul2` & `mult8`

### Part 1 — `mul2` (multiply by 2)

**RTL**

```verilog
// rtl/mult_2.v
module mul2(
    input  wire [7:0] a,
    output wire [8:0] y
);
    assign y = a * 2; // typically becomes a left shift by synthesis
endmodule
```

**What synthesis does:** typically rewrites multiply-by-2 to a `<<1` shift or optimized gates — extremely cheap.

> **Figure placeholder:** `figures/mult2_before_after.png` (RTL vs synthesized netlist visualization)

### Part 2 — `mult8` (8x8 multiplier)

**RTL**

```verilog
// rtl/mult_8.v
module mult8 (
    input  wire [7:0] a,
    input  wire [7:0] b,
    output wire [15:0] p
);
    assign p = a * b;
endmodule
```

**What synthesis does:** picks a multiplier architecture (array / Wallace / compressors) based on heuristics and optimization targets; much larger than `mul2`.

> **Figure placeholder:** `figures/mult8_impl.png` — compressor tree / partial product structure.

---

## Quick start — reproducible steps

1. **Place the SKY130 .lib** into `lib/` (e.g. `sky130_fd_sc_hd__tt_025C_1v80.lib`).
2. **Simulate** a DFF testbench:

   ```
   iverilog -o tb.vvp rtl/asyncres_dff.v tb/tb_dff_asyncres.v
   ./a.out
   gtkwave tb_dff_asyncres.vcd
   ```
3. **Synthesize** with Yosys:

   ```
   yosys -p "read_liberty -lib lib/sky130_fd_sc_hd__tt_025C_1v80.lib; read_verilog rtl/asyncres_dff.v; synth -top dff_asyncres; dfflibmap -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib; abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib; write_verilog -noattr out/dff_asyncres_net.v"
   ```
4. **Inspect** the `show`/SVG outputs and the generated netlist under `out/`.

---

## Tips & Gotchas (merged cheat-sheet + lecture points)

* Always pass the *same* `.lib` to `read_liberty` and `abc -liberty`. Mismatch → wrong mapping.
* Use **hierarchical** for large designs and debugging; flatten only for final whole-design optimizations or very small projects.
* **Avoid mixing** async & sync resets unless spec explicitly requires both — mapping varies by library.
* Use **non-blocking** assignments (`<=`) in sequential always blocks.
* For multiply-by-constant (like `*2`) expect shift or simpler logic post-synthesis.
* Flattening too early loses the hierarchical debug context — flatten only as needed.
* Check flip-flop polarities (active-high vs active-low) with `.lib` flop cell attributes.

---

## Files in this directory (recommended)

**Verilog sources**

* `rtl/asyncres_dff.v` (async reset DFF)
* `rtl/asyncset_dff.v` (async set DFF)
* `rtl/dff_syncres.v` (sync reset DFF)
* `rtl/asyncres_syncres.v` (hybrid)
* `rtl/mult_2.v`, `rtl/mult_8.v`
* `multiple_modules.v` (example top + submodules)

**Synthesis outputs / documentation**

* `multiple_modules_heir.v` — hierarchical netlist
* `multiple_modules_flat.v` — flattened netlist
* `notes.txt`, `output.svg`, screenshots (`ss-dff_*`) in `figures/`

---

## Key takeaways

* The SKY130 `.lib` files describe the PDK cell timing and are the authority for cell mapping. Choose the correct corner (tt/min/max) for the flow.
* Hierarchical vs flat synthesis: trade-off between modularity/debugging and whole-design optimization.
* Pick reset strategies (async, sync, hybrid) based on requirement and support from target library; code flops consistently and check mapping with `dfflibmap`.
* Always simulate before synthesis — verify that synthesized netlist behavior matches RTL expectations.

---
