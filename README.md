# 4x4 Systolic Array AI Accelerator — RTL-to-GDSII Flow

## Overview
This project implements an end-to-end digital ASIC design and physical implementation of a **4x4 Weight-Stationary Systolic Array AI Accelerator** in Verilog HDL. It accelerates matrix multiplication workloads using a grid of Processing Elements (PEs) with dedicated input activation skewing, taken from RTL through logic synthesis and Place & Route (PnR) to a fabrication-ready GDSII layout on the open-source **SkyWater 130nm (`sky130A`) PDK**.

## Architecture

### Dataflow & Execution Stages
1. **Weight Loading:** Stationary weights are pre-loaded into each Processing Element (`pe.v`) across the 4x4 array prior to compute cycles.
2. **Input Activation Skewing:** Dedicated delay registers (`skew.v`) stagger incoming activation rows diagonally to synchronize wavefront arrival at each PE.
3. **Systolic Multiply-Accumulate (MAC):** Each PE multiplies incoming activations with its stored stationary weight and accumulates partial sums vertically down the column.
4. **Output Collection:** Final accumulated matrix product results exit the bottom row of the 4x4 array (`sa_core.v` / `sa_top.v`).

### Features
- 4x4 Weight-Stationary Systolic Array datapath
- 16 Processing Elements (PEs) with local weight registers and MAC units
- Hardware input data-skewing registers for systolic wavefront alignment
- Modular, synthesizable Verilog RTL design
- Self-checking Verilog functional testbench
- Automated open-source RTL-to-GDSII flow (Yosys + OpenROAD + KLayout)
- SkyWater 130nm (`sky130_fd_sc_hd`) standard-cell implementation with 0 DRC violations
<<<<<<< HEAD
=======
- Interactive browser-based live demo ([`docs/live_demo.html`](docs/live_demo.html))
>>>>>>> 795c705 (Update README.md with live demo link in original format)

## RTL Modules

| Module | Description |
|---|---|
| `pe.v` | Processing Element (MAC unit with weight-stationary register) |
| `skew.v` | Input activation skewing registers for systolic wavefront alignment |
| `systolic_array.v` | 4x4 Processing Element mesh interconnect |
| `sa_core.v` | Core controller and systolic array integration |
| `sa_top.v` | Top-level chip wrapper (`sa_top`) |

## Project Structure

```text
systolic-array-ai-accelerator-main/
├── constraints/
│   └── sa_top.sdc           # Timing constraints (SDC)
├── docs/
<<<<<<< HEAD
│   └── layout_preview.png   # Post-PnR physical layout screenshot (KLayout)
=======
│   ├── layout_preview.png   # Post-PnR physical layout screenshot (KLayout)
│   └── live_demo.html       # Interactive browser-based live demo
>>>>>>> 795c705 (Update README.md with live demo link in original format)
├── results/
│   ├── pnr.log              # OpenROAD Place & Route execution log
│   ├── route_drc.rpt        # Post-routing DRC report (0 violations)
│   ├── sa_top.gds           # Final fabrication-ready GDSII layout
│   ├── synth.log            # Yosys synthesis log
│   └── timing.rpt           # Static Timing Analysis (STA) report
├── rtl/                     # Accelerator RTL modules
├── scripts/                 # Yosys, OpenROAD, and GDSII export scripts
├── tb/                      # Functional Verilog testbench
└── Makefile                 # Automated build, simulation, and PnR targets
```

## Simulation

Run these commands from the project root to execute RTL simulation:

```bash
make sim
```

*(Alternatively, run directly using Icarus Verilog and GTKWave:)*

```bash
iverilog -g2012 -o sim.out rtl/*.v tb/tb_sa.v
vvp sim.out
```

## Verification Results

The systolic array was simulated using a self-checking Verilog testbench (`tb/tb_sa.v`) to validate weight loading, diagonal activation skewing, and matrix multiplication output accuracy:

| Test Case | Verified Operation | Status |
|---|---|---|
| Reset & Initialization | Clean register state initialization | PASS |
| Weight Stationary Load | All 16 PE weight registers loaded accurately | PASS |
| Input Skew Alignment | Diagonal activation wavefront synchronization across rows | PASS |
| 4x4 Matrix Multiplication | Accumulated partial sums match expected reference matrix | PASS |
| Back-to-Back Compute | Consecutive matrix streams without pipeline stalls or hazards | PASS |

**Regression result: ALL VERIFICATION TESTS PASSED**

## Synthesis

The RTL was synthesized and mapped to the **SkyWater 130nm (`sky130_fd_sc_hd`)** standard-cell library using **Yosys** (`scripts/synth.ys` and `scripts/synth_flat.ys`).

* The synthesis log and gate-level statistics are available at [`results/synth.log`](results/synth.log).
* Timing constraints used during synthesis and physical design are defined in [`constraints/sa_top.sdc`](constraints/sa_top.sdc).

## Physical Design & Layout Evidence

The synthesized netlist was taken through floorplanning, placement, clock tree synthesis (CTS), global/detailed routing, and Static Timing Analysis (STA) using **OpenROAD** (`scripts/pnr.tcl`), followed by GDSII export (`scripts/export_gds.py`).

| Signoff Stage / Metric | Tool / Report | Result |
|---|---|---|
| **Process Node & Library** | SkyWater 130nm (`sky130A`) | `sky130_fd_sc_hd` |
| **Place & Route (PnR)** | OpenROAD (`results/pnr.log`) | Completed (Floorplan, CTS, Routing) |
| **Static Timing Analysis (STA)** | OpenSTA (`results/timing.rpt`) | Timing constraints met (`sa_top.sdc`) |
| **Design Rule Check (DRC)** | OpenROAD (`results/route_drc.rpt`) | **0 DRC Violations** (Clean) |
| **Final GDSII Artifact** | KLayout (`results/sa_top.gds`) | Verified standard-cell & routing geometry |

<<<<<<< HEAD
=======
### Post-PnR GDSII Layout (`sa_top.gds`)

![4x4 Systolic Array GDSII Layout](docs/layout_preview.png)
>>>>>>> 795c705 (Update README.md with live demo link in original format)

* **Full GDSII & Design Artifacts (Google Drive):** [Download / View `sa_top.gds` & Project Archive](https://drive.google.com/file/d/1kn9kp622gy0I48O0_KbN6Dc86Hj4ESli/view?usp=drivesdk)
* **Local GDSII Path:** `results/sa_top.gds`

## Scope and Limitations

- Implements a 4x4 weight-stationary systolic array optimized for dense matrix multiplication (`GEMM`) kernels.
<<<<<<< HEAD
- Input activations and stationary weights use fixed-width integer arithmetic as defined in the current RTL parameters.
=======
- Input activations and stationary weights use fixed-width integer arithmetic as defined in the RTL parameters.
>>>>>>> 795c705 (Update README.md with live demo link in original format)
- Physical design signoff covers RTL-to-GDSII synthesis, PnR, STA, and internal OpenROAD routing DRC (`0` violations) on `sky130A`.
