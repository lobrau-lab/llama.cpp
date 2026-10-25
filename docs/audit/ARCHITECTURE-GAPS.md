# Architecture Gaps - UAR-001 Phase 0

## Scope and Evidence Sources
This document evaluates the architectural assumptions made in the provisional ADR-000 against the evidence gathered during the Phase 0 reconnaissance.
Evidence is drawn from the Phase 0 reconnaissance script executed at timestamp `$(date -u +"%Y-%m-%dT%H:%M:%SZ")`.

## Findings

### Adapter Boundary (Subprocess HTTP vs. Linked C API)
- **Status:** `UNVERIFIED` (Assumed Viable)
- **Evidence:** `llama-server` is not installed, so we cannot verify the stability of its HTTP surface (`/v1/chat/completions`, `/metrics`, `/memory`). However, the repository structure indicates a standard `llama.cpp` codebase where these endpoints are typically stable.
- **Decision Deferred:** The decision to use a versioned process/HTTP boundary remains sound, as it provides the most isolation (invariant INV-004). Linking the C API is deferred until Phase 9 measurement.

### Metrics and Memory Planning
- **Status:** `UNVERIFIED`
- **Evidence:** We cannot test if `llama-server` metrics are sufficient for Phase 4 memory planning because the server is not built.
- **Decision Deferred:** We must defer finalizing the memory planning architecture until Phase 1 builds the server and we can actively probe the `/memory` endpoint.

### Model Discovery Path
- **Status:** `CONFIRMED`
- **Evidence:** Filesystem layout shows GGUF files residing in a flat or shallow directory structure (`./models/ggml-vocab-*.gguf`).
- **Architecture Implication:** UAR's model discovery should support configurable search roots and handle flat directories containing `.gguf` and `.safetensors` files.

### Multi-Engine Abstraction Requirements
- **Status:** `NOT TESTED`
- **Evidence:** Only `llama.cpp` related structures are present in the current workspace.
- **Architecture Implication:** While no other engines are present, UAR must treat engine extensibility as a required architectural property (invariant INV-002). The adapter layer must not hardcode `llama.cpp` specific concepts where a generic OpenAI API concept fits.

### Security Gaps (Phase 0 to Phase 2)
- **Status:** `INFERRED`
- **Evidence:** UAR will run as a standard process.
- **Architecture Implication:** To adhere to INV-004 (Execution isolation), UAR must ensure that when it launches `llama-server` as a subprocess, the arguments are strictly sanitized and never directly constructed from model outputs or untrusted network inputs.

### Unverified Assumptions
- **Workspace Structure:** The provisional ADR states UAR should be "Outside the `llama.cpp` source tree." The current working directory *is* a `llama.cpp` source tree. If UAR development occurs here, it will technically be *inside* the tree unless UAR is kept in an isolated subdirectory (e.g., `uar/`) or the `llama.cpp` code is moved to a submodule (e.g., `vendor/llama.cpp`).

## Implications for UAR Phase 1
- **ADR-000 Reconciliation:** The provisional ADR-000 is generally sound, but the repository placement decision ("Outside the `llama.cpp` source tree") needs clarification given the current workspace reality. The recommendation is to proceed with the HTTP adapter boundary and defer C API integration.
