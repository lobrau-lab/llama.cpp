# Dependency Risks - UAR-001 Phase 0

## Scope and Evidence Sources
This document evaluates the risks associated with toolchains, hardware drivers, disk space, and licensing.
Evidence is drawn from the Phase 0 reconnaissance script executed at timestamp `$(date -u +"%Y-%m-%dT%H:%M:%SZ")`.

## Findings

### Hardware & Acceleration Dependencies
- **NVIDIA / CUDA:** `UNAVAILABLE` - No NVIDIA drivers (`nvidia-smi`) or CUDA toolchain (`nvcc`) are present.
- **Risk (CUDA for Blackwell GPUs):** High risk if the target is an RTX 5070 (Blackwell) as stated in the work order. The current environment completely lacks the required CUDA 12.8+ toolchain and drivers. Any prebuilt binaries or builds on this machine will not support CUDA.
- **Prebuilt Binaries:** `UNAVAILABLE` - No prebuilt `llama.cpp` binaries exist.
- **Risk (Hardware Backend Mismatch):** Building `llama.cpp` locally on this machine will result in a CPU-only build. If this build is later deployed to the target hardware (RTX 5070), it will fail to utilize the GPU.

### Toolchain Availability
- **Rust/Go/C++:** `CONFIRMED` - The required toolchains (Rust, C++, CMake) are present and up to date. This mitigates the risk of being unable to build the UAR wrapper or the CPU fallback version of the engine.

### Disk Space
- **Capacity:** `CONFIRMED` - 93 GB available space on the root overlay.
- **Risk (Disk Space):** Low risk for small to medium models. 93 GB is sufficient for downloading multiple Q4_K_M quants of 8B-70B parameter models.

### Licensing Obligations
- **llama.cpp Licensing:** `CONFIRMED` - The repository contains an MIT `LICENSE` file. UAR must comply with attribution requirements if distributing binaries.
- **Backend Toolchains:** `NOT TESTED` - Since CUDA/ROCm/Vulkan are not installed, their specific licensing obligations (e.g., NVIDIA EULA) do not currently apply to this environment, but must be accounted for when deploying to the target hardware.

## Commands/Probes Used
- Hardware toolchain probes (`nvidia-smi`, `nvcc`, `vulkaninfo`, `rocm-smi`).
- General toolchain probes (`rustc`, `gcc`, `cmake`).
- Disk space probe (`df -hT .`).

## Known Gaps and Ambiguities
- The stark difference between the documented expected target (Ryzen + RTX 5070) and the actual probed environment (Xeon + No GPU) creates significant ambiguity. We must assume this is a CI/development container, not the final deployment target.

## Implications for UAR Phase 1
- Development in Phase 1 must assume a CPU-only backend for testing and validation within this environment.
- The build system for UAR must be flexible enough to handle cross-compilation or environment-specific compilation of `llama.cpp` to ensure the correct backend (CUDA) is targeted when finally deployed to the RTX 5070 hardware.
