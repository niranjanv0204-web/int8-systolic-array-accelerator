# INT8 Systolic Array Accelerator — RTL-to-GDSII Flow

## Overview
This project implements an end-to-end hardware design, verification, and physical implementation of a **4x4 INT8 Systolic Array AI Accelerator** and MNIST inference pipeline in Verilog HDL. It covers weight-stationary and streaming systolic compute cores, FC1/FC2 neural network inference stages, Python golden-model vector verification, and RTL-to-GDSII physical design flow.

## Architecture

### Dataflow & Execution Stages
1. **Weight Loading & Stationary Setup:** Stationary INT8 weights and stream parameters are configured across the 4x4 Processing Element (PE) grid prior to compute cycles.
2. **Systolic Compute & Wavefront Execution:** Activations and partial sums propagate through the 4x4 systolic array and PE mesh (, , , , , ).
3. **FC1 Tiling, Accumulation & Activation:** Tiled FC1 computations accumulate partial sums, run post-processing, apply ReLU activation, and requantize intermediate values (, , , , ).
4. **Classification & Top-Level Integration:** Final FC2 classification and top-level integration tie the systolic compute engine and inference datapath together (, , , ).

### Features
- 4x4 INT8 weight-stationary and streaming systolic array datapath
- Modular synthesizable Verilog RTL design for compute array and MNIST inference pipeline
- Python golden-model vector verification and multi-image MNIST end-to-end testbenches
- RTL-to-GDSII physical design artifacts including GDSII, DEF, post-layout Verilog netlist, and timing report

## RTL Modules

| Module | Description |
|---|---|
| `systolic_array_4x4.v` | 4x4 INT8 systolic array core |
| `systolic_array_ws_4x4.v` | 4x4 weight-stationary systolic array variant |
| `weight_stationary_array_4x4.v` | Weight-stationary 4x4 array datapath |
| `weight_stationary_array_4x4_fsm.v` | Control FSM for weight-stationary 4x4 array |
| `weight_stationary_pe.v` | Weight-stationary Processing Element (PE) |
| `ws_pe_stream.v` | Streaming weight-stationary PE module |
| `fc1_controller.v` | FC1 layer tiling and execution controller |
| `fc1_tile_accumulator.v` | FC1 tile partial-sum accumulator |
| `fc1_postprocess.v` | FC1 post-processing stage |
| `relu.v` | ReLU activation block |
| `requantize.v` | Fixed-point/INT8 requantization block |
| `fc2_classifier.v` | FC2 classification layer |
| `mnist_accel_v2_top.v` | MNIST accelerator v2 top module |
| `mnist_accelerator_top.v` | MNIST accelerator top module |
| `accelerator_top.v` | Top-level accelerator wrapper |

## Running Simulation (Icarus Verilog)

Compile active RTL modules (excluding `*_pre_*_fix.v` backup files) with your target testbench:

```bash
iverilog -o /tmp/tb_mnist_end_to_end.vvp \
  src/rtl/accelerator_top.v \
  src/rtl/fc1_controller.v \
  src/rtl/fc1_postprocess.v \
  src/rtl/fc1_tile_accumulator.v \
  src/rtl/fc2_classifier.v \
  src/rtl/mnist_accel_v2_top.v \
  src/rtl/mnist_accelerator_top.v \
  src/rtl/relu.v \
  src/rtl/requantize.v \
  src/rtl/systolic_array_4x4.v \
  src/rtl/systolic_array_ws_4x4.v \
  src/rtl/weight_stationary_array_4x4.v \
  src/rtl/weight_stationary_array_4x4_fsm.v \
  src/rtl/weight_stationary_pe.v \
  src/rtl/ws_pe_stream.v \
  testbench/tb_mnist_end_to_end.v && \
vvp /tmp/tb_mnist_end_to_end.vvp
```

## Verification Results

The design was verified across unit, Python golden-vector, and multi-image MNIST testbenches:

| Testbench | Metric / Scope | Result |
|---|---|---|
| `testbench/tb_systolic_4x4.v` | Functional checks | **432 / 432** passed (0 failed) |
| `testbench/tb_python_vectors.v` | Python golden vectors (25,088 INT32 comparisons; 20-cycle FSM completion timeout) | **1568 / 1568** passed |
| `testbench/tb_mnist_end_to_end.v` | 4-image MNIST end-to-end inference checks | **172 / 172** passed (0 failed) |
| `testbench/tb_mnist_100_images.v` | 100-image MNIST suite (25 batches, 39,200 FC1 tile transactions) | **1125 / 1125** passed (**98 / 100**, **98%** accuracy) |

## Physical Design Deliverables

Saved physical layout artifacts in `~/Crucible/workspace/systolic4/backup_results/`:

| Deliverable / Metric | Details | Value / Status |
|---|---|---|
| `sa_top.gds` | Physical layout GDSII stream | 12 MB |
| `sa_top.def` | Physical layout DEF design exchange | 12 MB |
| `sa_top_final.v` | Final post-layout Verilog netlist | 2.3 MB |
| `timing.rpt` | Static timing report (2.3 KB) | **8.01** slack |

## Scope and Limitations

- Implements a 4x4 INT8 systolic array and FC1/FC2 MNIST inference datapath in Verilog HDL.
- Active simulation runs compile non-backup RTL modules (`src/rtl/*.v`) excluding `*_pre_*_fix.v` files.
- Physical design artifacts (`sa_top.gds`, `sa_top.def`, `sa_top_final.v`, and `timing.rpt`) are archived in `~/Crucible/workspace/systolic4/backup_results/`.
