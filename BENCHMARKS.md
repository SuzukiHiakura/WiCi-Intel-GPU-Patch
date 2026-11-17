# Capacity and functional benchmark

The purpose is to run large models through SSD expert streaming with limited resident memory. Both models returned coherent replies to the same original chicken-pot-pie request on the Intel Arc Pro B70.

| Model | Output | Total | Prefill | Avg / low / peak tokens/s | Sampled peak local VRAM |
|---|---:|---:|---:|---:|---:|
| GPT-OSS 120B | 512 | 27.4 min | 5.4 min | 0.3906 / 0.2526 / 0.8206 | 6.21 GiB |
| DeepSeek V4.1 Flash 1017 (mixed quantization) | 512 | 69.2 min | 20.5 min | 0.1761 / 0.1531 / 0.1867 | 10.75 GiB |

Both replies hit the 512-token allowance; neither ended with EOS. They are useful functional results, not complete recipes or cooking-accuracy validation. Exact saved text: [original prompt](RECIPE-PROMPT.txt), [GPT response](GPT120B-RESPONSE.txt), [DeepSeek response](DEEPSEEK-RESPONSE.txt).

GPT host/GPU caches were 512 MiB/2 GiB; DeepSeek used 8 MiB/64 MiB. Whole-system RAM load peaked at 44%/57%, respectively, on a 64 GiB machine; other applications were running. DeepSeek native private commit peaked at 11.93 GiB, a separate measure from physical resident RAM or VRAM.

| Model | Logical source / H2D / D2D totals | End-to-end source / H2D / D2D rates |
|---|---:|---:|
| GPT-OSS 120B | 764.8 / 764.1 / 1055.7 GiB | 476.1 / 475.6 / 657.1 MiB/s |
| DeepSeek V4.1 Flash 1017 (mixed quantization) | 3036.0 / 3034.3 / 3034.3 GiB | 748.9 / 748.5 / 748.5 MiB/s |

These are logical cache/transfer counters divided by the full owned run time. They do not measure instantaneous PCIe bandwidth, unique data, physical NAND traffic or SSD writes. Decode rates exclude startup, prefill, first token and EOS; model control/reasoning tokens are included. Different tokenizers, caches and backend versions mean this is not a controlled speed ranking.

Measured system: Windows 10 Education 22H2, build 19045.6456, i7-8700K (6 cores/12 threads), MSI Z390-A PRO, 2×32 GiB RAM at configured 3200 MHz, Arc Pro B70 driver 32.0.101.8976. Intel UHD 630 was also enumerated; inference was B70-only. The RX 6800 was not enumerated in this benchmark Windows session. Full nonidentifying inventory: [HARDWARE.json](HARDWARE.json).
