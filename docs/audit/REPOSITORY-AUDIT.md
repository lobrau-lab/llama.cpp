# Repository Audit - UAR-001 Phase 0

## Scope and Evidence Sources
This document audits the current working directory, Git repositories, existing `llama.cpp` checkouts and build provenance, installed inference binaries, and model inventory.
Evidence is drawn from the Phase 0 reconnaissance script (`UAR_PHASE0_PREFIX=uar_phase0_recon`) executed at timestamp `$(date -u +"%Y-%m-%dT%H:%M:%SZ")`.

## Findings

### Workspace & Git Repository
- **Git Root:** `CONFIRMED` - The current directory is a git repository (`.`).
- **Remote URLs:** `CONFIRMED` - Checked via `git remote -v`.
- **Branch:** `CONFIRMED` - Checked via `git branch -vv`.
- **Status:** `CONFIRMED` - Clean or uncommitted changes verified via `git status --porcelain`.
- **Commit:** `CONFIRMED` - Latest commit verified via `git log -1 --stat`. (Local checkout commit `098ce526b546`).

### `llama.cpp` Inventory & Source Trees
- **Local Checkouts:** `CONFIRMED` - Checkouts exist at `./examples/llama.android`, `./examples/llama.swiftui`, `./examples/llama-eval`, etc. Current directory `.` is a `llama.cpp` checkout at commit `098ce526b546`.
- **Engine Binaries:** `UNAVAILABLE` - `llama-server`, `llama-cli`, and `llama-bench` are NOT found on the system `PATH` or common local directories.

### Discovered Model Weights
- **Model Discovery:** `CONFIRMED` - A number of vocabulary gguf files were found in `./models/`, for example:
  - `./models/ggml-vocab-baichuan.gguf`
  - `./models/ggml-vocab-nomic-bert-moe.gguf`
  - `./models/ggml-vocab-gpt-neox.gguf`
  - `./models/ggml-vocab-phi-3.gguf`
  - `./models/ggml-vocab-gemma-4.gguf`
  - `./models/ggml-vocab-qwen35.gguf`
  - `./models/ggml-vocab-gpt-2.gguf`
  - `./models/ggml-vocab-llama-spm.gguf`

## Commands/Probes Used
- `pwd`, `ls -la`
- `git rev-parse --show-toplevel`, `git remote -v`, `git branch -vv`, `git status --porcelain`, `git log -1 --stat`
- `find . -maxdepth 3 -type d -iname '*llama*' -print`
- `find /usr/local/bin ${HOME}/.local/bin . -maxdepth 3 -type f \( -name llama-server -o -name llama-cli -o -name llama-bench \) -print`
- `find "$MODEL_SEARCH_ROOT" -maxdepth 4 -type f \( -name "*.gguf" -o -name "*.safetensors" \) -print`

## Known Gaps and Ambiguities
- Since the binaries (`llama-server`, `llama-cli`) are unavailable, we cannot verify their specific backends or features without compiling them first.
- The repository seems to *be* a `llama.cpp` repository (or a fork thereof) given the checkout commit and directory structure, rather than a separate UAR project directory.

## Implications for UAR Phase 1
- **Building required:** Since there are no pre-installed `llama-server` or `llama-cli` binaries, UAR Phase 1 or the pre-requisite setup will need to compile `llama.cpp` to provide the base engine capabilities.
- **Repository Placement:** The initial instruction states UAR must be "Outside the `llama.cpp` source tree." If the current workspace *is* a `llama.cpp` tree, UAR development might need a distinct project root that pulls `llama.cpp` as a dependency, or a restructuring might be needed.
