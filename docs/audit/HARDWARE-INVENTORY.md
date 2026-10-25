# Hardware Inventory - UAR-001 Phase 0

## Scope and Evidence Sources
This document records the hardware topology, RAM, GPU, storage, and available acceleration/compute toolchains for the target environment.
Evidence is drawn from the Phase 0 reconnaissance script executed at timestamp `$(date -u +"%Y-%m-%dT%H:%M:%SZ")`.

## Findings

### Host Hardware & OS Topology
- **CPU:** `CONFIRMED` - Intel(R) Xeon(R) Processor @ 2.30GHz. 1 socket, 4 cores per socket, 1 thread per core (4 threads total).
- **NUMA Topology:** `CONFIRMED` - 1 NUMA node.
- **L3 Cache:** `CONFIRMED` - 45 MiB.
- **AVX-512 Support:** `UNAVAILABLE` - AVX is present in flags, but AVX-512 specific flags are not explicitly listed in the truncated output (has avx, avx2).
- **RAM Total/Available:** `CONFIRMED` - 7.8 GiB Total / 7.4 GiB Available.
- **Storage:** `CONFIRMED` - 98 GB total space on overlay root, 93 GB available (1% used).

### Acceleration & Compute Runtimes
- **NVIDIA/CUDA:** `UNAVAILABLE` - `nvidia-smi` and `nvcc` are not found. No `/usr/local/cuda*` directories exist.
- **Vulkan:** `UNAVAILABLE` - `vulkaninfo` is not found.
- **ROCm/AMD:** `UNAVAILABLE` - `rocm-smi` is not found.
- **GPU Summary:** `UNAVAILABLE` - No discrete GPU or driver tools (NVIDIA/AMD) are accessible in this environment.
- **Memory Domains:** `NOT TESTED` - Since no discrete GPU is found, the statement that host RAM and GPU VRAM are separate memory domains on discrete-GPU systems does not apply to this specific environment, though it remains a UAR invariant.

## Commands/Probes Used
- `lscpu | grep -E 'Model name|Socket|Core|Thread|MHz|L1|L2|L3|NUMA|Flags'`
- `free -b`, `free -h`
- `df -hT .`
- `nvidia-smi`, `nvcc`, `vulkaninfo`, `rocm-smi`

## Known Gaps and Ambiguities
- The target environment specified in the work order is a Ryzen 7 7800X3D with 64GB RAM and an RTX 5070. The actual environment is an Intel Xeon with 8GB RAM and no accessible GPU.
- Due to the lack of GPU tools, we cannot test or confirm any CUDA/ROCm/Vulkan capabilities.

## Implications for UAR Phase 1
- **CPU-Only Execution:** The UAR must gracefully fall back to CPU-only execution in this environment.
- **Resource Constraints:** With only ~7.4 GiB of available RAM, large models cannot be loaded. Phase 1 must target small models (e.g., small quantizations of Qwen, Llama3 8B, or smaller) for testing.
