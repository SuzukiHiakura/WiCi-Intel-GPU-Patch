# Capacity and functional benchmark

Both models returned coherent replies to the same original chicken pot pie request on an Intel Arc Pro B70. The purpose was to demonstrate large-model functionality with limited resident GPU memory through SSD expert streaming.

## Run times and output

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

Both runs stopped at the **local 512-output-token limit**, before EOS. This was a benchmark setting, not a model limit or an inference failure. The replies are incomplete; recipe completeness and cooking accuracy were not evaluated.

Read the [original prompt](RECIPE-PROMPT.txt), [GPT response](GPT120B-RESPONSE.txt) and [DeepSeek response](DEEPSEEK-RESPONSE.txt).

## Memory and cache settings

| Memory setting or measurement | GPT-OSS 120B | DeepSeek V4.1 Flash 1017 |
|---|---:|---:|
| Configured host expert cache | 512 MiB | 8 MiB |
| Configured GPU expert cache | 2 GiB | 64 MiB |
| Peak sampled process VRAM | 6.21 GiB | 10.75 GiB |
| Peak whole-system RAM load | 44% | 57% |

The computer had **64 GiB of RAM**, and other applications were running. Whole-system RAM load includes those applications. The cache settings are budgets, not total model memory use.

DeepSeek's sampled native private commit peaked at **11.93 GiB**. Private commit measures committed process memory; it is separate from resident RAM and VRAM. Peak sampled process VRAM is the highest observed local GPU-memory use for the process.

## Data movement

| Model | Transfer counter | Total (GiB) | Average over full run (MiB/s) |
|---|---|---:|---:|
| GPT-OSS 120B | Logical source reads | 764.8 | 476.1 |
| GPT-OSS 120B | Host-to-device (H2D) | 764.1 | 475.6 |
| GPT-OSS 120B | Device-to-device (D2D) | 1055.7 | 657.1 |
| DeepSeek V4.1 Flash 1017 | Logical source reads | 3036.0 | 748.9 |
| DeepSeek V4.1 Flash 1017 | Host-to-device (H2D) | 3034.3 | 748.5 |
| DeepSeek V4.1 Flash 1017 | Device-to-device (D2D) | 3034.3 | 748.5 |

These counters include repeated transfers. Rates are totals divided by the full run time. They do not measure unique model data, instantaneous PCIe bandwidth, physical SSD reads or SSD writes.

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

The RX 6800 was not enumerated in this benchmark Windows session. This describes the recorded session, not a general inventory of owned hardware.

The full-precision values and run metadata are in [BENCHMARK.json](BENCHMARK.json).
