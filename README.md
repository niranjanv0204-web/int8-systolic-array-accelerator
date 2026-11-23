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
- Interactive browser-based live demo ([`docs/live_demo.html`](docs/live_demo.html))

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
│   ├── layout_preview.png   # Post-PnR physical layout screenshot (KLayout)
│   └── live_demo.html       # Interactive browser-based live demo
├── results/
│   ├── pnr.log              # OpenROAD Place & Route execution log
│   ├── route_drc.rpt        # Post-routing DRC report (0 violations)
