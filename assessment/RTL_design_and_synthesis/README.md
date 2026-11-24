# RTL Design & Logic Synthesis — SKY130A PDK

This directory contains lab screenshots and verification outputs for **RTL Design, Functional Simulation, and Logic Synthesis** completed as part of the **Women in Semiconductors: VLSI Design Engineer Program (570 Hours)** conducted by **VSD – SFAL – ESSCI – Marvell**.

## Module Scope & Workflow

1. **Day 1 — Introduction to Verilog RTL Design & Logic Synthesis:**
   - Simulated combinational multiplexer RTL designs (`good_mux.v`) and testbenches using **Icarus Verilog (`iverilog`)** and inspected signal waveforms in **GTKWave**.
   - Explored standard-cell library concepts (fast vs. slow cells, drive strengths) and performed initial logic synthesis mapped to the SkyWater 130 nm PDK (`sky130_fd_sc_hd`) using **Yosys** and **ABC**.

2. **Day 2 — Timing `.lib` Files, Hierarchical vs. Flat Synthesis & Flop Coding Styles:**
   - Analyzed Liberty (`.lib`) timing files across PVT corners, boolean pin functions, and 2D Non-Linear Delay Model (NLDM) lookup tables (`sky130_fd_sc_hd__a2111o`).
   - Compared **Hierarchical Synthesis** (`multiple_modules_hier.v`) versus **Flat Synthesis** (`flatten` / `multiple_modules_flat.v`) and sub-module synthesis.
   - Simulated and synthesized D flip-flops with asynchronous reset (`dff_asyncres.v`), asynchronous set (`dff_async_set.v`), and synchronous reset (`dff_syncres.v`) using `dfflibmap`, along with constant-multiplication hardware optimizations (`mult_2.v`, `mult_8.v`).

3. **Day 3 — Combinational & Sequential Logic Optimizations:**
   - Verified Boolean simplification and constant propagation across combinational modules (`opt_check.v` – `opt_check4.v`) and cross-boundary flattening (`multiple_module_opt.v`, `multiple_module_opt2.v`).
   - Analyzed sequential constant propagation (`dff_const1.v` – `dff_const5.v`) and unused-state pruning in binary counters (`counter_opt.v`, `counter_opt2.v`).

4. **Day 4 — Gate-Level Simulation (GLS), Blocking vs. Non-Blocking & Synthesis-Simulation Mismatch:**
   - Executed end-to-end **Gate-Level Simulation (GLS)** on synthesized netlists (`ternary_operator_mux_net.v`) using Sky130 behavioral and primitive models (`primitives.v`, `sky130_fd_sc_hd.v`).
   - Investigated and reproduced **synthesis-simulation mismatches** caused by incomplete sensitivity lists (`bad_mux.v`) and improper blocking assignment ordering (`blocking_caveat.v`).

5. **Day 5 — Synthesis Optimizations: `if`/`case` Constructs & `for`/`generate` Loops:**
   - Analyzed priority logic vs. parallel multiplexers and identified unintended latch inference (`sky130_fd_sc_hd__dlxtp_1`) in incomplete `if-else` and `case` statements (`incomp_if.v`, `incomp_if2.v`, `incomp_case.v`, `partial_case_assign.v`) vs. complete designs (`comp_case.v`).
   - Reproduced overlapping `case` synthesis-simulation mismatches (`bad_case.v`) via GLS.
   - Implemented parameterized and structural hardware using procedural `for` loops and `genvar` + `for-generate` constructs (`mux_generate.v`, `demux_generate.v`, and multi-bit Ripple Carry Adder `rca.v` instantiating `fa.v`).

## Tools & PDK Used

- **PDK & Standard-Cell Library:** SkyWater 130 nm (`sky130_fd_sc_hd`)
- **RTL Simulation & Waveforms:** Icarus Verilog (`iverilog`), GTKWave
- **Logic Synthesis & Visualization:** Yosys, ABC, Graphviz (`xdot`)

