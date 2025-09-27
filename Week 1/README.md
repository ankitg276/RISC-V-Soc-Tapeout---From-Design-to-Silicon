# 🚀 RISC‑V Tapeout Program — **Week 1**: Verilog, Synthesis & Optimization (Intro)

> **Goal (Week 1):** Gain a strong conceptual foundation in Verilog RTL, simulation, synthesis, and early RTL optimizations for the SkyWater (sky130) flow. This README is an **introductory** document — detailed commands, step‑by‑step lab instructions and runnable examples will be placed in separate **day‑wise README.md** files under each `dayX/` folder.

---

## 📋 Table of contents

1. [Overview & Objectives](#overview--objectives)
2. [Prerequisites & Setup](#prerequisites--setup)
3. [High‑level workflow (no commands)](#high-level-workflow-no-commands)
4. [Day‑wise plan & topic explanations](#day-wise-plan--topic-explanations)
5. [Repository structure & suggestions](#repository-structure--suggestions)
6. [Style & best practices (short)](#style--best-practices-short)
7. [Where to find lab commands & examples](#where-to-find-lab-commands--examples)

---

## 🧭 Overview & Objectives

This week introduces the core concepts and workflows used in an open‑PDK tapeout environment using the SkyWater sky130 process. The emphasis of this introduction file is conceptual clarity — what each tool does, why we use it, and what you should learn from each day.

**Key concepts covered during the week:**

* Verilog RTL structure and semantics (combinational vs sequential, module boundaries, parameterization)
* Functional simulation to verify behavior before synthesis
* Logic synthesis to map RTL to gates/primitives
* Common synthesis pitfalls and how they lead to simulation mismatches
* Early RTL optimization techniques (combinational, sequential, coding style)

**Learning outcomes:**

* Understand how and why simulation, synthesis, and gate‑level flows fit together in a tapeout pipeline
* Recognize constructs that synthesize poorly or cause mismatches
* Know how to read synthesis reports at a high level and where to look for common problems

---

## 🛠️ Prerequisites & Setup

Recommended environment: **Ubuntu (WSL2)** or native Linux. Windows users should prefer WSL2 for tool compatibility. Tools used in Week 1 labs include Icarus Verilog (iverilog), GTKWave, Yosys, and the SkyWater sky130 PDK.

**Quick install (Ubuntu / WSL):**

```bash
sudo apt-get update
sudo apt-get install -y iverilog gtkwave yosys git make
```

> Note: The course may require additional PDK/OpenLane/OpenROAD setup for later labs — those steps will be documented inside the specific day‑wise lab README files and the course installer notes.

---

## ▶️ High‑level workflow (no commands)

This introduction describes the conceptual flow used in the labs (the precise commands and scripts live in the day‑wise readmes):

* **Design & RTL:** Implement modules in Verilog using clear interfaces, parameters for widths, and self‑contained units that are easy to test.
* **Testbench & Simulation:** Create testbenches that exercise corner cases and produce waveform output for debugging. The goal is functional verification — confirming the design behaves as intended.
* **Synthesis:** Use a logic synthesis tool to transform RTL into a gate‑level netlist that approximates how the design will appear in silicon. Pay attention to synthesis reports: cell counts, inferred structures, and warnings.
* **GLS & Mismatch Checking:** Optionally perform gate‑level simulation (GLS) using the synthesized netlist to detect functional mismatches introduced by synthesis. Investigate the causes (inferred latches, X propagation, reset differences).
* **Iterate & Optimize:** Based on simulation and synthesis observations, apply RTL changes to eliminate anti‑patterns, improve area/timing, and reduce unexpected inference.

---

## 📆 Day‑wise plan & topic explanations

Below are detailed explanations of the topics for each day. The **lab steps, commands, expected outputs and examples** for each topic will be provided inside the corresponding `dayX/README.md` files.

### **DAY 1 — Verilog RTL design & Simulation**

**Core topics explained:**

* **Verilog RTL fundamentals:** Modules, ports, parameters, continuous assignments, procedural blocks. Distinguish between behavioral descriptions (for simulation/verification) and structural descriptions (for mapping to gates).
* **Testbench principles:** How to write self‑checking testbenches, stimulus generation, and organizing tests to clearly show pass/fail conditions.
* **Waveform analysis:** What waveforms reveal — signal timing relationships, glitches, and unexpected X values.
* **Synthesis intro:** What synthesis does conceptually — translating RTL into gates, inferring registers and simple optimizations.

**Lab focus (conceptual):** Create simple, self‑contained modules (e.g., mux, adder), write testbenches that assert correct behavior, and view waveforms to validate timing and logic.

---

### **DAY 2 — Timing libraries, Hierarchical vs Flat Synthesis & Flop coding styles**

**Core topics explained:**

* **Timing libraries (.lib):** What information a liberty file contains — cell timing arcs, pin capacitances, timing checks — and why they matter later in physical design and STA.
* **Hierarchical vs Flat synthesis:** Benefits and tradeoffs of preserving or flattening hierarchy — modularity vs global optimization, debugability vs resource and runtime considerations.
* **Flop / register coding styles:** Different ways to code registers (synchronous vs asynchronous reset, enable usage, clock gating), how synthesis interprets those styles, and the optimization implications for timing and power.

**Lab focus (conceptual):** Explore different register encodings and observe how they appear post‑synthesis and how they affect optimization opportunities.

---

### **DAY 3 — Combinational and Sequential Optimizations**

**Core topics explained:**

* **Combinational logic optimizations:** Constant propagation, dead‑code elimination, expression simplification, and how bitwise/vector encodings affect logic generation.
* **Sequential optimizations:** Register sharing, retiming, and trimming unused outputs; how choice of state encoding and register placement impacts area and timing.

**Lab focus (conceptual):** Compare synthesized results of naive vs optimized implementations to learn what coding patterns reduce gate count and improve timing.

---

### **DAY 4 — GLS, Blocking vs Non‑blocking & Synth‑Sim mismatch**

**Core topics explained:**

* **Blocking (`=`) vs non‑blocking (`<=`) semantics:** Behavioral effects in sequential code, ordering implications in simulation, and best practices to avoid race conditions.
* **Synthesis‑simulation mismatch causes:** Why inferred latches, uninitialized signals, or different reset behaviors lead to mismatches—how to identify and correct them.
* **Gate‑level simulation (GLS):** Purpose and limitations; GLS helps find problems that only appear after synthesis but should be used judiciously due to complexity.

**Lab focus (conceptual):** Introduce scenarios where mismatches occur and discuss how to refactor RTL to be synthesis‑friendly and simulation‑predictable.

---

### **DAY 5 — Optimization in synthesis**

**Core topics explained:**

* **IF / CASE semantics in synthesis:** Priority vs parallel semantics, how defaults and coverage affect latch inference, and how to write explicit, synthesis‑friendly constructs.
* **Incomplete & overlapping cases:** Why omission of branches can create unintended state (latches) and how to prevent that with defaults or explicit assignments.
* **For / generate usage:** Parameterized replication of logic and when to use generate constructs for scalable hardware description.

**Lab focus (conceptual):** Exercise incomplete/overlapping constructs and `for`/`generate` patterns to see how synthesis maps them to hardware and to learn defensive coding techniques.

---

## 📁 Suggested repository structure

```
week1/
  README.md                # (this introduction file)
  day1/                    # contains day1/README.md with commands and lab steps
  day2/
  day3/
  day4/
  day5/
```

Each `dayX/README.md` will contain the detailed lab steps, commands, example files, expected outputs, and troubleshooting tips. This file intentionally stays conceptual and reader‑friendly.

---

## 🧩 Style & best practices (short)

* Prefer non‑blocking updates for sequential logic to avoid simulation races.
* Avoid inferred latches by providing explicit default assignments in combinational blocks.
* Use parameters for widths and constants to make modules reusable and easier to test.
* Keep testbenches deterministic and self‑checking where possible.

---

## 📚 Where to find lab commands & examples

All runnable commands, full example files, and step‑by‑step instructions will be located inside the `day1/`, `day2/`, ... `day5/` folders as separate `README.md` files. That separation keeps this top‑level introduction clean and focused on concepts.

---

*Prepared for: RISC‑V Tapeout Program — Week 1*
*Author: Ankit Kumar Gupta*
