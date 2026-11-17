Developed with GPT Sol 6.1

# WiCi/InferSSD Intel GPU patch

**Functional on the tested Intel Arc Pro B70 with GPT-OSS120B and DeepSeek V4.1 Flash1017.**

This project provides the Intel/B70 Windows Vulkan patch for WiCi/InferSSD and the DeepSeek compatibility layer used for the recorded functional proof.

## B70 functional test results

The purpose is large-model functionality with a small resident GPU-memory footprint through SSD expert streaming. Throughput is documented as the tradeoff. Both models produced coherent replies through WiCi/InferSSD on the B70.

| Tested model | Input / output tokens | Total time | Prefill | Avg / low / peak tokens/s | Peak sampled local VRAM |
|---|---:|---:|---:|---:|---:|
| GPT-OSS 120B | 273 /512 | 27.4 min | 5.4 min | 0.3906 / 0.2526 / 0.8206 | 6.21 GiB |
| DeepSeek V4.1 Flash 1017 (mixed quantization) | 211 /512 | 69.2 min | 20.5 min | 0.1761 / 0.1531 / 0.1867 | 10.75 GiB |

GPT host/GPU expert caches:512MiB/2GiB. DeepSeek:8MiB/64MiB. Whole-system RAM load peaked at44%/57%, respectively, on the64GiB system, with other applications running. DeepSeek sampled native private commit peaked at11.93GiB; private commit, resident RAM and VRAM are separate measures.

Decode rates exclude startup, prefill, first token and EOS; model control/reasoning tokens are included. Different tokenizers and cache budgets mean this is not a controlled speed ranking. Full computer/transfer counters: [BENCHMARKS.md](BENCHMARKS.md) and [BENCHMARK.json](BENCHMARK.json).

## Models used

- **DeepSeek-V4.1-Flash-1017**, architecture `deepseek41`: ten-shard GGUF named `DeepSeek-V4.1-Flash-1017-Q8_0-00001-of-00010.gguf` through `00010`. This is mixed quantization, with native MXFP4 routed experts rather than an entirely8-bit model. [Matching GGUF files](https://huggingface.co/mrfakename/DeepSeek-V4.1-Flash-1017-GGUF/tree/main/Q8_0) · [base model](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017).
- **GPT-OSS120B Derestricted**, tested file `gpt-oss-120b-Derestricted.MXFP4_MOE.gguf`. [Recorded quantizer repository](https://huggingface.co/mradermacher/gpt-oss-120b-Derestricted-GGUF) · [matching single-file listing](https://huggingface.co/gghfez/gpt-oss-120b-Derestricted.MXFP4_MOE-gguf) · [fine-tune source](https://huggingface.co/ArliAI/gpt-oss-120b-Derestricted) · [base model](https://huggingface.co/openai/gpt-oss-120b).

The links were checked on November17,2026. The original model files were held and version-checked during testing. The GPT file previously matched its recorded published LFS SHA256 and size; DeepSeek was header/version/custody-bound without a full payload checksum comparison. This does not claim publisher authenticity. Weights are obtained from their publishers and retain their own licenses.

## Tested computer

Windows10 Education22H2, build **19045.6456**,64-bit; MSI **Z390-A PRO(MS-7B98)**; Intel **Core i7-8700K**,6cores/12threads at3.70GHz; **2×32GiB RAM**, configured3200MHz; **Intel Arc Pro B70**, driver **32.0.101.8976**. The neural path used the B70/Vulkan F32 route. Intel UHD630 was also enumerated. [Hardware inventory](HARDWARE.json).

## Original recipe prompt and recorded responses

**Both replies were cut off by the local benchmark's512-output-token limit. Neither model reached EOS before that cap. This cutoff is a test setting, not an inference failure.** The successful result is coherent generated text through the Intel GPU backend; complete recipes and cooking accuracy were not separately evaluated.

<details>
<summary>Original user prompt</summary>

````text
I wanted you to share with me a recipe of a chicken pot pie. Mine usually uses a frozen bag of mixed veggies, chicken breast I cook and shred myself, then a simple filling mixture of butter, salt, pepper, garlic, flour, and chicken stock. Total time, sadly, is about an hour and fourty minutes to get the filling fully prepped, about 40 minutes of baking the chicken, and if I properly pace myself I might cut it down to about an hour and twenty minutes, maybe 10, but it is difficult. Following that, the homemade pie crust, top and bottom, then the sealing, foil over the edge, baking, par baking, final baking, all several steps that add to time and tend to make it either an exceptional chore taking 2 and a half hours or near 4 to make the pie, and by that time, i am quite hungry, and eat the entire pie, or most of it... don't even get it time to cool to properly maintain its structure.
````

[Exact saved text](RECIPE-PROMPT.txt)

</details>

<details>
<summary>GPT-OSS120B response</summary>

````text
## Quick‑&‑Easy Chicken Pot‑Pie (≈ 1 hour total)

Below is a **“speed‑up” version of the classic chicken‑pot‑pie** that keeps the flavor you love but slashes the prep‑and‑bake time in half.  
The trick is to use a **store‑bought flaky crust**, **pre‑cooked rotisserie chicken**, and a **one‑pan “sauce‑first” method** that eliminates the separate par‑bake step.  

---

### What you’ll need  

| Category | Ingredients | Qty (serves 4‑6) |
|----------|-------------|-----------------|
| **Crust** | All‑butter flaky pie crust (2‑roll pack) – bottom & top | 1 pack (≈ 2 × 9‑in. crusts) |
| **Chicken** | Rotisserie chicken (or leftover cooked chicken) | 2 cups shredded |
| **Veggies** | Frozen mixed vegetables (peas, carrots, corn, green beans) | 2 cups |
| **Sauce** | Unsalted butter | 3 Tbsp |
| | All‑purpose flour | 3 Tbsp |
| | Low‑sodium chicken broth | 1 ½ cups |
| | Milk (or half‑and‑half) | ½ cup |
| | Garlic, minced | 2 tsp |
| | Onion powder | ½ tsp |
| | Dried thyme | ½ tsp |
| | Salt & freshly cracked black pepper | to taste |
| **Finishing** | 1 egg, beaten (optional, for glaze) | 1 |
| | Fresh parsley, chopped (optional) | 2 Tbsp |

---

### Equipment  

* 9‑in. pie dish (or 2‑qt casserole dish)  
* Large skillet or saucepan (2‑qt)  
* Rolling pin (if you need to trim the crust)  
* Pastry brush (for egg wash)  

---

## Step‑by‑Step (45 min active, 15 min bake)

### 1. Prep the crust (5 min)
1. **Unroll** the bottom crust, gently press it into the pie dish, and **trim** any
````

[Exact saved text](GPT120B-RESPONSE.txt)

</details>

<details>
<summary>DeepSeek V4.1 Flash1017 response</summary>

````text
# Streamlined Chicken Pot Pie (with make-ahead shortcuts)

Your process is solid — the problem is **sequencing**, not the recipe. You're doing everything serially on one day. Here's how to get a great pot pie in ~90 minutes active-ish time, or ~20 minutes on bake day if you prep ahead.

## The two big time sinks, fixed

**1. Chicken:** Stop baking breasts (40 min). Poach them instead — 12–15 minutes, and the poaching liquid becomes your stock.
- 1.5 lb chicken breast, 4 cups water + 1 cup stock or bouillon, bay leaf, peppercorns, half an onion.
- Simmer gently 12–15 min until 165°F. Rest 5 min, shred. **Strain the liquid — that's your stock.** Zero waste, better flavor than the bag.

**2. Crust:** Make it the night before (or weeks before and freeze). One batch, 10 minutes, fridge overnight. Roll cold — it handles better and you're not doing it hungry.

## Filling (one pan, ~20 min)

- 4 tbsp butter, 1 diced onion, 2 diced carrots, 2 celery stalks → sauté 6–8 min
- 4 tbsp flour → cook 1 min
- 2½ cups hot poaching stock + ½ cup milk → whisk, simmer until thick (3–4 min)
- Season: salt, pepper, garlic, thyme, a pinch of poultry seasoning
- Stir in shredded chicken + **1 cup frozen peas** (add peas last so they stay bright — the rest of the veg is fresh, which is the single biggest flavor upgrade over the frozen bag, and it costs you ~5 extra minutes of knife work)

## Assembly & bake

- Bottom crust, filling, top crust, seal, **cut vents**
- **Skip the par-bake and the foil dance.** 425°F for 30–35 min. If the edge browns too fast, a pie shield or foil ring goes on at minute 20 — one step, not a regimen.
- **Rest 15 minutes.** I know. But this is the difference between a slice and a landslide — and it's the one step where waiting genuinely pays.

## The actual schedule

| When | Task | Time |
|---|---|---|
| Night before | Make crust dough, fridge | 10 min |
| Night before (optional) |
````

[Exact saved text](DEEPSEEK-RESPONSE.txt)

</details>

## Source patch application

Base: [WiCi/InferSSD](https://github.com/wici-ai/inferssd) commit `aa957de2958e8e6df84f87da49b8b4d55484b5bb`. Use a clean checkout. The two patches are staged as Intel/B70 support followed by DeepSeek-specific compatibility.

```powershell
git clone https://github.com/wici-ai/inferssd.git
Set-Location inferssd
git checkout aa957de2958e8e6df84f87da49b8b4d55484b5bb
git -c core.autocrlf=false apply ../WiCi-Intel-GPU-Patch/0001-WiCi-Intel-B70.patch
git -c core.autocrlf=false apply ../WiCi-Intel-GPU-Patch/0002-DeepSeek-V4.1-Flash-1017.patch
```

The DeepSeek second patch is the same source delta provided in the [separate model-specific project](https://github.com/SuzukiHiakura/DeepSeek-V4.1-Flash-1017-B70-Patch). The Intel-only first stage has not been separately benchmarked as a newly rebuilt release; the recorded results come from the tested local model-specific builds. Source patch application and final-file hashes are checked separately from runtime testing. A clean portable rebuild has not been run on another machine. [Build configuration](BUILD-NOTES.md).

## Development and license

Approximate development: **November13–17,2026**; final DeepSeek correction and successful test: **November17,2026**. Recorded goal activity was about25hours39minutes, including builds, runs and waits; engineering-only time was not separately measured. This review copy was prepared at `2026-11-17T19:52:55.260783+00:00`.

GNU **GPL-3.0-or-later** project/patch distribution, with original Apache-2.0 and MIT notices preserved. [GNU license](LICENSE) · [upstream notices](NOTICE) · [third-party licenses](THIRD-PARTY.md).
