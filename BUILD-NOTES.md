# Build and benchmark configuration

The patch targets WiCi/InferSSD's bundled engine. Windows x64 C++17, Windows SDK, CMake/Ninja and Vulkan shader tools were used locally. The engine exposes `GGML_VULKAN` and `BUILD_SHARED_LIBS`; follow the pinned upstream build layout. The successful proof used a finite native bridge harness over the SSD expert-stream backend, rather than every upstream application surface.

Runtime: B70-only neural F32 path, F32 KV, Flash Attention off, context4096, input allowance1024, output allowance512, batch64. GPT used microbatch32/full SWA/512MiB host and2GiB GPU caches. DeepSeek used microbatch1/rolling SWA/8MiB host and64MiB GPU caches. The process job cap was12GiB; local/nonlocal GPU ceilings24GiB/1GiB with1GiB WDDM headroom. Existing CPU/RAM/admission/identity/cleanup protections were retained. Both tests reached the local512-output limit successfully.

The public source reconstruction separates F32 softplus from unchanged upstream F16 generation, normalizes line endings and makes a test root portable. These public build adaptations are recorded in provenance; source-application checks do not claim a clean public binary rebuild. The measured results remain those of the exact successful local builds. Other Intel GPUs, different drivers, quantizations and context settings require their own validation.
