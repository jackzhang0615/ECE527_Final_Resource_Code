# Static/Dynamic Data Partitioning for GEMM on Pynq-Z2 (CPU + FPGA)

This repository contains the hardware handoff file (Vivado export) and the host-side Python experiments used to evaluate **static** and **dynamic** row-wise data partitioning for **GEMM (C = A·B)** on a **Zynq SoC (Pynq-Z2)**. The FPGA implements a tiled GEMM accelerator (Vitis HLS IP), and the CPU runs a NumPy GEMM baseline; collaborative modes split the output rows between CPU and FPGA.

---

## Repository Contents

- **`GEMM_Static_without_irregularities/`**  
  Scripts for **regular GEMM** experiments (no injected per-row irregular cost). Includes:
  - CPU-only baseline
  - FPGA-only baseline
  - CPU+FPGA static α sweep
  - Calibration-based α estimation (if included in the script)

- **`GEMM_Dynamic_under_Irregularities/`**  
  Scripts for **irregular GEMM** experiments using a per-row workload profile `work[]` to emulate non-uniform kernels. Includes:
  - Static α sweep under irregularity
  - Calibration-based dynamic split (e.g., using first 10% of rows)
  - Task-pool dynamic scheduling (with `block_rows` granularity)

- **`gemm_design_wrapper_2048.xsa`**  
  Vivado **hardware handoff export** for the full SoC design (Zynq PS + GEMM HLS IP). This packages the hardware configuration and metadata needed by software.  
  > Note: Many PYNQ flows commonly use a `.bit` + `.hwh` overlay pair. If your PYNQ version does not load `.xsa` directly, re-export the design to obtain `.bit/.hwh` and update the script’s `BITFILE_PATH`.

- **`xilinx_com_hls_gemm_accel_1.0.zip`**  
  Packaged Vitis HLS IP (Vivado-compatible) for the GEMM accelerator. Useful if you want to re-integrate or rebuild the Vivado block design.

---

## Requirements

Hardware / platform:
- **Pynq-Z2** (or compatible Zynq-7000 board with PYNQ)
- A working **PYNQ image** on the board (so that `pynq` Python package is available)

Python packages (typically already present on PYNQ):
- `pynq`
- `numpy`

---

## How to Run

1. **Copy this repository onto the PYNQ board** (e.g., under `~/jupyter_notebooks/`).

2. **Edit the overlay path in the script** (if needed).  
   Each script contains something like:
   ```python
   BITFILE_PATH = "/home/xilinx/jupyter_notebooks/GEMM_project/gemm_design_wrapper_2048.xsa"
   ol = Overlay(BITFILE_PATH)
