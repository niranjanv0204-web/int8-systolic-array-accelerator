# INT8 Systolic Array AI Accelerator

A 4×4 INT8 weight-stationary systolic-array hardware accelerator (16 MACs/cycle) for MNIST edge inference (`784-32-10`), verified bit-exactly against a Python golden model and taken through post-PnR physical design deliverables (`results/sa_top.gds`, `results/timing.rpt`), along with an interactive browser demo (`docs/live_demo.html`).

## Architecture Highlights
- **Compute Array:** 4×4 weight-stationary Processing Element (PE) grid performing 16 signed INT8 MACs per cycle with 32-bit accumulation.
- **Dataflow & Control:** Input skew logic, weight/input buffers, and an FSM controller (`LOAD` / `STREAM` / `DRAIN`) with tiling support for multi-layer FC inference.
- **Post-Processing:** Hardware ReLU activation and Q31 requantization back to INT8.

## Verification & Physical Design Summary
- **RTL & Golden Model Verification:**
  - `tb_systolic_4x4.v`: 432/432 checks passed
  - `tb_python_vectors.v`: 1,568/1,568 test vectors passed (25,088 INT32 comparisons)
  - `tb_mnist_end_to_end.v`: 172/172 checks passed
  - `tb_mnist_100_images.v`: 1,125/1,125 checks passed (98% inference accuracy)
- **Post-PnR Deliverables:**
  - Layout GDSII: `results/sa_top.gds`
  - Timing Report: `results/timing.rpt` (worst slack: `8.01`)

## Interactive Demo
- **Repository File:** [`docs/live_demo.html`](docs/live_demo.html) (Download and open in any browser to test MNIST digits and inspect the 4×4 systolic wavefront)
- **GitHub Pages URL (after enabling Pages from `/docs`):** [Open Live Browser Demo](https://niranjanv0204-web.github.io/int8-systolic-array-accelerator/live_demo.html)

## Team
- **Authors:** Niranjan Vishwakarma, Bilal Ahmad
