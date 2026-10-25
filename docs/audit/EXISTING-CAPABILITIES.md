# Existing Capabilities - UAR-001 Phase 0

## Scope and Evidence Sources
This document evaluates the existing capabilities of `llama.cpp` and maps them to UAR requirements.
Evidence is drawn from the Phase 0 reconnaissance script executed at timestamp `$(date -u +"%Y-%m-%dT%H:%M:%SZ")`.

## Findings

### Base Capabilities
- **Text Generation Endpoint:** `UNVERIFIED` - `llama-server` is not installed, so endpoints (`/v1/chat/completions`, `/v1/completions`) cannot be actively probed. However, standard `llama.cpp` (which this repository appears to be) is known to support these.
- **GPU Layer Offload and Split Modes:** `UNVERIFIED` - `llama-server` is unavailable. Assuming compilation from source, `-ngl` and split modes are standard features, but cannot be confirmed without a build.
- **KV-Cache and Context Configuration:** `UNVERIFIED` - Cannot be confirmed without built binaries.
- **Batch Processing:** `UNVERIFIED` - Cannot be confirmed without built binaries.
- **Quantization:** `UNVERIFIED` - The repository contains python scripts for conversion (e.g., `convert_hf_to_gguf.py`), implying quantization support is part of the ecosystem, but binary support (`llama-quantize`) cannot be tested.
- **Metrics and Memory Reporting:** `UNVERIFIED` - `/metrics` and `/memory` endpoints cannot be tested.
- **Model Metadata Extraction:** `UNVERIFIED` - Cannot test GGUF metadata availability via `llama.cpp` binaries directly.
- **Cancellation and Process Lifecycle:** `UNVERIFIED` - HTTP cancellation behavior cannot be tested.

### Toolchains & Build Systems
- **Rust Toolchain:** `CONFIRMED` - rustc 1.94.0, cargo 1.94.0 installed.
- **C/C++ Toolchain:** `CONFIRMED` - gcc 13.3.0, clang 18.1.3, cmake 3.28.3, make 4.3, ninja 1.11.1 installed.
- **Go Toolchain:** `CONFIRMED` - `go` binary exists (version flag error but present).

## Commands/Probes Used
- Probes for `llama-server`, `llama-cli`, `llama-bench`.
- Toolchain probes (`rustc`, `gcc`, `cmake`, etc.).

## Known Gaps and Ambiguities
- Since the binaries are not pre-compiled or available on the `PATH`, we cannot verify the operational status of any `llama.cpp` capability directly. We must infer them based on the repository contents (which is a `llama.cpp` codebase) and standard knowledge, but mark them `UNVERIFIED` per Phase 0 rules.

## Implications for UAR Phase 1
- **Capability Bootstrapping:** Phase 1 will require compiling `llama-server` (likely using the available `cmake` and `gcc`/`clang` toolchains) to actually leverage and verify these capabilities.
- **UAR Value Add:** UAR must fill the gap of abstracting this underlying engine (once built) into a stable, managed lifecycle. UAR will handle multi-engine orchestration, assuming responsibility for spinning up `llama-server` processes and routing traffic, relying on `llama-server` for the actual OpenAI-compatible HTTP surface.
