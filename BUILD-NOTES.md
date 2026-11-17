# Build and benchmark settings

## Build environment

The patch targets the engine bundled with WiCi/InferSSD.

- Windows x64 and C++17.
- Windows SDK, CMake, Ninja and Vulkan shader tools.
- Engine options include `GGML_VULKAN` and `BUILD_SHARED_LIBS`.
- Use the pinned upstream revision and patch order shown in the README.

The successful runs used a native test harness connected to the SSD expert-streaming backend. Other upstream application interfaces were not separately validated.

## Shared runtime settings

| Setting | Value |
|---|---|
| Inference GPU and backend | Intel Arc Pro B70; Vulkan F32 neural path |
| KV cache precision | F32 |
| Flash Attention | Off |
| Context | 4,096 tokens |
| Maximum input allowance | 1,024 tokens |
| Maximum output allowance | 512 tokens |
| Batch size | 64 |
| Process job memory cap | 12 GiB |
| Local GPU-memory ceiling | 24 GiB |
| Nonlocal GPU-memory ceiling | 1 GiB |
| WDDM headroom | 1 GiB |

## Model-specific settings

| Setting | GPT-OSS 120B | DeepSeek V4.1 Flash 1017 |
|---|---:|---:|
| Microbatch | 32 | 1 |
| Sliding-window attention (SWA) | Full | Rolling |
| Host expert cache | 512 MiB | 8 MiB |
| GPU expert cache | 2 GiB | 64 MiB |

Existing CPU, RAM, admission, file-identity and cleanup protections were retained. Both runs successfully reached the local 512-token output limit.

## Public source preparation

The public patch reconstruction:

- Separates F32 softplus from unchanged upstream F16 shader generation.
- Normalizes line endings.
- Makes a test-output directory portable.

These adaptations are recorded in [SOURCE-PROVENANCE.json](SOURCE-PROVENANCE.json). Patch-application checks are separate from a clean public binary rebuild, which has not been performed. The reported measurements come from the successful local builds.

Other Intel GPUs, drivers, quantizations and context settings require their own validation.
