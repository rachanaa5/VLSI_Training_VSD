# RTL Design & Logic Synthesis — SKY130 PDK

This directory documents the hands-on labs for **RTL Design, Functional Simulation, and Logic Synthesis** completed as part of the **Women in Semiconductors: VLSI Design Engineer Program (570 Hours)** conducted by **VSD – SFAL – ESSCI – Marvell**.

Each day has its own folder with a README that walks through the Verilog RTL, the exact commands that were run, the captured screenshots, and what each result shows. All screenshots are stored in [`images/`](images/).

---

## Workshop Progress

| Day | Topics Covered | Documentation |
|---|---|---|
| Day 1 | Verilog RTL & testbenches, Icarus Verilog + GTKWave simulation, first Yosys synthesis of a 2:1 MUX (`good_mux`) | [Day 1 →](Day_1/README.md) |
| Day 2 | Liberty `.lib` timing files, hierarchical vs. flat synthesis, sub-module synthesis, flip-flop coding styles | [Day 2 →](Day_2/README.md) |
| Day 3 | Combinational & sequential logic optimisations — constant propagation, boundary flattening, unused-logic removal | [Day 3 →](Day_3/README.md) |
| Day 4 | Gate-Level Simulation (GLS), synthesis–simulation mismatch, blocking vs. non-blocking assignments | [Day 4 →](Day_4/README.md) |
| Day 5 | `if` / `case` constructs, unintended latch inference, procedural `for` loops and `for-generate` | [Day 5 →](Day_5/README.md) |

Click a day to open its detailed README and screenshots.

---

## Tools & PDK Used

- **PDK & Standard-Cell Library:** SkyWater 130 nm, high-density library `sky130_fd_sc_hd` (typical corner `tt_025C_1v80`)
- **RTL Simulation & Waveforms:** Icarus Verilog (`iverilog` / `vvp`), GTKWave
- **Logic Synthesis & Visualisation:** Yosys 0.52, ABC, Graphviz (`xdot`)
- **Editor:** Vim / gVim
- **Platform:** Ubuntu on WSL

## Working Directory & Library Paths

All labs were run from the `verilog_files/` directory of the workshop repository:

```bash
cd ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

| Item | Path (relative to `verilog_files/`) |
|---|---|
| Standard-cell Liberty file | `../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib` |
| Verilog models for GLS | `../my_lib/verilog_model/primitives.v`, `../my_lib/verilog_model/sky130_fd_sc_hd.v` |

## Repository Structure

```text
RTL_design_and_synthesis/
├── README.md        ← this index
├── Day_1/README.md  ← Verilog, iverilog/GTKWave, first Yosys run
├── Day_2/README.md  ← .lib files, hier vs. flat, flip-flops
├── Day_3/README.md  ← logic optimisations
├── Day_4/README.md  ← GLS, mismatches, blocking vs. non-blocking
├── Day_5/README.md  ← if/case, latches, for / for-generate
└── images/          ← all screenshots, named Day_<day>.<n>.png
```

## Key Learning Areas

- Writing synthesizable Verilog RTL and self-contained testbenches
- Simulating with Icarus Verilog and inspecting `.vcd` waveforms in GTKWave
- Reading Liberty (`.lib`) files: PVT corners and 2-D lookup tables
- Synthesizing RTL to Sky130 standard cells with Yosys + ABC (`synth`, `dfflibmap`, `abc`, `flatten`, `opt_clean -purge`)
- Hierarchical vs. flat netlists and sub-module synthesis
- Asynchronous vs. synchronous set/reset flip-flops and how they map to Sky130 cells
- Combinational constant propagation and sequential optimisation (unused flops removed)
- Gate-Level Simulation and the synthesis–simulation mismatches caused by incomplete sensitivity lists, blocking-assignment ordering, and overlapping `case` items
- Latch inference from incomplete `if` / `case` statements, and `for` / `for-generate` hardware
