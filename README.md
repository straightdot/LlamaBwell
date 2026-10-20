# LlamaBwell
llama.cpp optimized for GPU on Blackwell arch along with some new features for dual GPU systems.

Based on llama.cpp release b11243 (commit `fc07d78`).

**Downloads**

- `llamaADALL-v4.13.1-win-cuda13.3-x64.zip`: the server and tools, with the CUDA (cuBLAS) and Visual C++ runtime files.
- `llamaADALL-v4.13.1-win-cuda13.3-x64-source.zip`: the source of exactly these binaries, with build instructions (BUILD.md).

**What is in this**

- **RTX 30, 40 and 50 cards**, tuned on an RTX 5080: about 1.5-2x normal llama.cpp's generation speed on a growing chat
  (8K-124K), and 1.4-1.5x its prompt speed.
- **N-gram drafting before MTP.** Copy and edit tasks (code, quoting, reprinting) generate 2-2.4x faster; prose is
  unchanged.
- **Two GPUs: `-sm kv`.** When the KV cache does not fit next to the weights, its older part (with the attention over it)
  moves to a second card, sized automatically. A 14.6 GB IQ4_XS model runs at a 237K context with a q8_0 KV cache on an
  RTX 5080 + RTX 4060 Ti. The vision encoder also moves to the second card when the first one is full, so images keep
  their full detail at large contexts.

  
- **Flags**
  - `-sm none` (the default) never splits and stops with a hint when the model does not fit;
  - `-sm kv` turns on the spill; KV goes to other(helper) GPU
  - `--ckpt-mem auto|host|helper` places the context checkpoints to other(helper) GPU/Sys RAM
  - `--mmproj-host 0|1|2` places the image projector's weights to other(helper) GPU
  - backend sampling is on by default (`--no-backend-sampling` turns it off).
    

**Notes**

- `-sm none` is the default (normal llama.cpp defaults to `-sm layer`). Multi-GPU users who want the layer split must pass
  `-sm layer`.
- `-sm row` is not available.
- Tested on Windows 10 with an RTX 5080 (model) and an RTX 4060 Ti (second card).
