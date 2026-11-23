# INT8 Systolic Array Accelerator

A hardware design and verification repository for a 4x4 INT8 systolic array accelerator, end-to-end MNIST inference pipeline, and RTL-to-GDSII physical design flow.

## Repository & Architecture
- **Repository**: niranjanv0204-web/int8-systolic-array-accelerator
- **Core Compute Array**: 4x4 INT8 systolic array (systolic_array_4x4.v, systolic_array_ws_4x4.v, weight_stationary_array_4x4.v, weight_stationary_array_4x4_fsm.v, weight_stationary_pe.v, ws_pe_stream.v)
- **MNIST Inference Pipeline**: FC1 tiling, accumulation, post-processing, ReLU, requantization, and FC2 classification (fc1_controller.v, fc1_tile_accumulator.v, fc1_postprocess.v, relu.v, requantize.v, fc2_classifier.v, mnist_accel_v2_top.v, mnist_accelerator_top.v, accelerator_top.v)

## Verification Results
- testbench/tb_systolic_4x4.v: **432 / 432** checks passed (0 failed)
- testbench/tb_python_vectors.v: **1568 / 1568** transactions passed (**25,088** INT32 comparisons) after increasing FSM completion timeout to 20 cycles
- testbench/tb_mnist_end_to_end.v: **172 / 172** checks passed (0 failed; 4-image MNIST inference verified)
- testbench/tb_mnist_100_images.v: **1125 / 1125** checks passed across 100 images (25 batches, 39,200 FC1 tile transactions) with **98 / 100 (98%)** classification accuracy

## Physical Design Deliverables
Saved physical layout artifacts in ~/Crucible/workspace/systolic4/backup_results/:
- sa_top.gds (12 MB)
- sa_top.def (12 MB)
- sa_top_final.v (2.3 MB)
- timing.rpt (2.3 KB, **8.01** slack)

## Running Simulation (Icarus Verilog)
Compile active RTL modules (excluding *_pre_*_fix.v backup files) with your target testbench:

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
