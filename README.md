Developed with GPT Sol 6.1

# WiCi/InferSSD Intel GPU patch

**Functional on the tested Intel Arc Pro B70 with GPT-OSS 120B and DeepSeek V4.1 Flash 1017.**

This project adds Intel GPU support to WiCi/InferSSD on Windows through Vulkan. Both models produced coherent responses in measured runs on the B70.

SSD expert streaming limits resident GPU-memory use. Slow generation is the tradeoff; the measured speed and memory use are shown below.

## B70 test results

| Model | Input tokens | Output tokens | Total run time | Prompt processing |
|---|---:|---:|---:|---:|
| GPT-OSS 120B | 273 | 512 | 27.4 min | 5.4 min |
| DeepSeek V4.1 Flash 1017 | 211 | 512 | 69.2 min | 20.5 min |

Total run time includes startup and prompt processing. Prompt processing, also called **prefill**, is the input-processing stage before generation.

### Generation speed

| Model | Average (tokens/s) | Lowest recorded (tokens/s) | Highest recorded (tokens/s) |
|---|---:|---:|---:|
| GPT-OSS 120B | 0.3906 | 0.2526 | 0.8206 |
| DeepSeek V4.1 Flash 1017 | 0.1761 | 0.1531 | 0.1867 |

These are decode speeds: they exclude startup, prefill, the first token and end-of-sequence (EOS). Model control and reasoning tokens are included.

These runs used different tokenizers, cache budgets and backend versions, so they are not a controlled speed comparison.

### Memory and cache settings

| Memory setting or measurement | GPT-OSS 120B | DeepSeek V4.1 Flash 1017 |
|---|---:|---:|
| Configured host expert cache | 512 MiB | 8 MiB |
| Configured GPU expert cache | 2 GiB | 64 MiB |
| Peak sampled process VRAM | 6.21 GiB | 10.75 GiB |
| Peak whole-system RAM load | 44% | 57% |

The computer had **64 GiB of RAM**, and other applications were running. Whole-system RAM load includes those applications. The cache settings are budgets, not total model memory use.

DeepSeek's sampled native private commit peaked at **11.93 GiB**. Private commit measures committed process memory; it is separate from resident RAM and VRAM. Peak sampled process VRAM is the highest observed local GPU-memory use for the process.

Full measurements: [benchmark report](BENCHMARKS.md) and [raw benchmark data](BENCHMARK.json).

## Models used

### DeepSeek V4.1 Flash 1017

- **Version:** DeepSeek-V4.1-Flash-1017, architecture `deepseek41`.
- **Files:** Ten GGUF shards, from `DeepSeek-V4.1-Flash-1017-Q8_0-00001-of-00010.gguf` through `00010`.
- **Quantization:** A mixed distribution with native MXFP4 expert weights. The Q8_0 filename does not mean every weight is 8-bit.
- **Downloads:** [Matching GGUF files](https://huggingface.co/mrfakename/DeepSeek-V4.1-Flash-1017-GGUF/tree/main/Q8_0).
- **Source:** [DeepSeek base model](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017).

### GPT-OSS 120B Derestricted

- **Tested file:** `gpt-oss-120b-Derestricted.MXFP4_MOE.gguf`.
- **Downloads:** [Matching single-file listing](https://huggingface.co/gghfez/gpt-oss-120b-Derestricted.MXFP4_MOE-gguf) and [recorded quantizer repository](https://huggingface.co/mradermacher/gpt-oss-120b-Derestricted-GGUF).
- **Sources:** [Derestricted fine-tune](https://huggingface.co/ArliAI/gpt-oss-120b-Derestricted) and [OpenAI base model](https://huggingface.co/openai/gpt-oss-120b).

Links were checked on **November 17, 2026**. Model weights are downloaded separately and retain their publishers' licenses.

### Model-file verification

- **GPT:** The file previously matched the recorded published LFS SHA-256 checksum and file size.
- **DeepSeek:** Files were checked by their headers and versions and tracked through testing. A full payload checksum comparison was not performed.

These checks describe file identity, not a guarantee of publisher authenticity.

## Tested computer

| Component | Tested configuration |
|---|---|
| Operating system | Windows 10 Education 22H2, 64-bit; build **19045.6456** |
| Motherboard | MSI Z390-A PRO (MS-7B98) |
| Processor | Intel Core i7-8700K; 6 cores / 12 threads; 3.70 GHz |
| Memory | 64 GiB: 2 × 32 GiB; configured at 3200 MHz |
| Inference GPU | Intel Arc Pro B70 |
| GPU driver | 32.0.101.8976 |
| Inference backend | Vulkan, F32 neural path |

Intel UHD 630 was also detected. Inference used the B70 exclusively. See the [hardware inventory](HARDWARE.json) for the recorded details.

## Original recipe prompt and recorded responses

**Both runs generated 512 output tokens and stopped at the benchmark's local limit before EOS.** The cutoff was a test setting, not an inference failure or the models' maximum reply length.

The saved replies are therefore incomplete. Recipe completeness and cooking accuracy were not evaluated.

<details>
<summary>Original user prompt</summary>

````text
I wanted you to share with me a recipe of a chicken pot pie. Mine usually uses a frozen bag of mixed veggies, chicken breast I cook and shred myself, then a simple filling mixture of butter, salt, pepper, garlic, flour, and chicken stock. Total time, sadly, is about an hour and fourty minutes to get the filling fully prepped, about 40 minutes of baking the chicken, and if I properly pace myself I might cut it down to about an hour and twenty minutes, maybe 10, but it is difficult. Following that, the homemade pie crust, top and bottom, then the sealing, foil over the edge, baking, par baking, final baking, all several steps that add to time and tend to make it either an exceptional chore taking 2 and a half hours or near 4 to make the pie, and by that time, i am quite hungry, and eat the entire pie, or most of it... don't even get it time to cool to properly maintain its structure.
````

[Exact saved text](RECIPE-PROMPT.txt)

</details>

<details>
<summary>GPT-OSS 120B response</summary>

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
<summary>DeepSeek V4.1 Flash 1017 response</summary>

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

## Applying the patches

Use a clean checkout of [WiCi/InferSSD](https://github.com/wici-ai/inferssd).

1. Check out the pinned upstream revision shown below.
2. Apply `0001-WiCi-Intel-B70.patch` for Intel GPU support.
3. Apply `0002-DeepSeek-V4.1-Flash-1017.patch` for DeepSeek compatibility.

```powershell
git clone https://github.com/wici-ai/inferssd.git
Set-Location inferssd
git checkout aa957de2958e8e6df84f87da49b8b4d55484b5bb
git -c core.autocrlf=false apply ../WiCi-Intel-GPU-Patch/0001-WiCi-Intel-B70.patch
git -c core.autocrlf=false apply ../WiCi-Intel-GPU-Patch/0002-DeepSeek-V4.1-Flash-1017.patch
```

The second patch is also distributed by the [DeepSeek Intel GPU patch project](https://github.com/SuzukiHiakura/DeepSeek-V4.1-Flash-1017-Intel-GPU-Patch). **Apply the DeepSeek stage once.**

### Validation scope

- The recorded local builds successfully ran both models on the B70.
- Patch application and final source-file hashes were checked separately.
- The newly separated Intel-only stage has not been benchmarked as a fresh release build.
- A clean portable rebuild on another machine has not been performed. Other Intel GPUs and model variants are unverified.

See [build and benchmark settings](BUILD-NOTES.md).

## Development and license

- **Approximate development period:** November 13–17, 2026.
- **Successful DeepSeek correction and test:** November 17, 2026.
- **Recorded goal activity:** About 25 hours 39 minutes, including builds, test runs and waits. Engineering-only time was not tracked separately.
- **Preview prepared:** November 17, 2026, at 19:52 UTC. The exact timestamp is retained in [REVIEW-STATUS.json](REVIEW-STATUS.json).

New project work and patch distribution use **GNU GPL version 3 or later**. Original Apache 2.0 and MIT notices are preserved.

- [GNU GPL license](LICENSE)
- [Upstream notices](NOTICE)
- [Third-party license information](THIRD-PARTY.md)
