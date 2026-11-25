# RTL Day 1 — Introduction to Verilog RTL Design and Logic Synthesis

---

# 1-RTL_D1SK1 — Introduction to Open-Source Simulator Icarus Verilog & Yosys

## Overview

This section introduces the core open-source Electronic Design Automation (EDA) toolchain used throughout the **SKY130 RTL Design & Synthesis** workflow:
- **Icarus Verilog (`iverilog` / `vvp`):** Open-source Verilog simulation and compilation engine used to verify functional correctness of RTL designs against testbenches.
- **GTKWave:** Waveform viewer used to inspect Value Change Dump (`.vcd`) signal transitions across simulation time.
- **Yosys & ABC:** Open-source RTL synthesis suite used to translate behavioral Verilog into gate-level netlists mapped to the SkyWater 130 nm standard-cell library (`sky130_fd_sc_hd`).

---

## Key Concepts

### RTL Design-to-Synthesis Flow

```text
Verilog RTL (.v) + Testbench (tb_*.v)
                ↓
   Icarus Verilog Simulation (iverilog / ./a.out)
                ↓
   Waveform Verification in GTKWave (.vcd)
                ↓
   Yosys RTL Synthesis + ABC Technology Mapping (.lib)
                ↓
   Gate-Level Netlist & Schematic (sky130_fd_sc_hd)



Design vs. Testbench:

The Design Under Test (DUT) (good_mux.v) contains synthesizable behavioral hardware logic (always @(*)).

The Testbench (tb_good_mux.v) has no input/output ports; it instantiates the DUT (uut), drives stimulus onto reg inputs, and dumps signal waveforms ($dumpfile / $dumpvars).

2-RTL_D1SK2 — Labwork Using Icarus Verilog & GTKWave (good_mux)
Overview
Demonstrates writing, simulating, and verifying a 2:1 combinational multiplexer (good_mux.v) using Icarus Verilog and GTKWave.

Lab Execution & Screenshots
Step 1: Inspecting RTL Design (good_mux.v) & Testbench (tb_good_mux.v)
Verilog
```c
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else
        y <= i0;
end
endmodule
```
Explanation:

Top split (good_mux.v): Implements a 2:1 multiplexer using always @(*) so that output y responds immediately to any change on i0, i1, or sel (y = sel ? i1 : i0).

Bottom split (tb_good_mux.v): Declares reg i0, i1, sel for input stimulus, wire y for observing output, and instantiates good_mux uut.

Step 2: Running Pre-Synthesis RTL Simulation & Viewing Waveforms
```bash
cd ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
iverilog good_mux.v tb_good_mux.v
./a.out
gtkwave tb_good_mux.vcd
```
Explanation:

When sel = 0 (0 ns to 75 ns, 150 ns to 225 ns), output y follows input i0 identically.

When sel = 1 (75 ns to 150 ns, 225 ns to 300 ns), output y tracks input i1 with zero latch behavior, confirming clean combinational MUX functionality.

3-RTL_D1SK3 & D1SK4 — Introduction to Standard-Cell Libraries & Logic Synthesis
Overview
Covers the fundamentals of standard-cell libraries (.lib files, fast vs. slow cells, drive strengths) and executes the complete Yosys + ABC logic synthesis flow on good_mux.v using the SkyWater 130 nm PDK (sky130_fd_sc_hd__tt_025C_1v80.lib).

Yosys Synthesis Commands
```bash
cd ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
yosys
```
Inside the yosys> prompt:

Tcl
```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog good_mux.v
synth -top good_mux
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
Where:

read_liberty -lib → Loads timing and functional definitions of Sky130 standard cells at typical corner (tt, 25°C, 1.80 V).

read_verilog → Parses the RTL source file.

synth -top good_mux → Performs generic RTL elaboration and high-level logic optimization.

abc -liberty → Maps generic Boolean logic gates onto physical Sky130 standard cells.

show → Generates and displays the gate-level schematic in Dot Viewer (xdot).

Gate-Level Synthesis Schematic & Explanation
Explanation:

Yosys and ABC synthesize the behavioral if(sel) RTL description into a single physical standard cell: sky130_fd_sc_hd__mux2_1.

Inputs i0, i1, and sel connect directly to standard-cell pins A0, A1, and S, driving output pin X to primary output y (1 cell total).

Key Learning Outcomes
After completing Day 1, the learner understands:

how to write clean combinational Verilog RTL (always @(*)) and testbenches

how to simulate designs with iverilog and verify signal behavior in gtkwave

how Yosys and ABC synthesize behavioral Verilog into Sky130 standard-cell gate-level schematics (sky130_fd_sc_hd__mux2_1)