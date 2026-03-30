# Running AI Locally with llama.cpp

## Introduction

To explore running AI models at a lower level, I tested llama.cpp — a lightweight C++ implementation that allows large language models to run directly from the terminal.

Unlike tools like LM Studio, this approach is more hands-on and gives better insight into how local inference actually works.

The goal was to understand the setup process, performance, and overall usability on mid-range hardware.

---

## Installation

The binaries were downloaded from the official llama.cpp GitHub releases page.

![Download page](llamacpp_ss/release_page.png)

Steps followed:
- Downloaded the Windows CUDA build
- Extracted the ZIP file
- Moved the extracted folder to `C:\llama.cpp`

The setup process was manual but straightforward.

---

## File Structure

After extraction, the folder contained multiple `.exe` and `.dll` files.

![Files](llamacpp_ss/files_llamacpp.png)

Important executables:
- `llama-cli.exe` → used for running models
- Other `.exe` files for benchmarking and utilities

This reflects the low-level nature of llama.cpp compared to GUI-based tools.

---

## Model Setup

Instead of downloading a model again, I reused the model from LM Studio.

Original location:

```
C:\Users\sharj\.lmstudio\models\lmstudio-community\Mistral-7B-Instruct-v0.3-GGUF\Mistral-7B-Instruct-v0.3-Q4_K_M.gguf
```

The model file was copied into:

```
C:\llama.cpp\models\
```

This confirms that GGUF models are portable and can be reused across tools.

---

## Running the Model

The model was launched using the following command:

```
llama-cli.exe -m models\Mistral-7B-Instruct-v0.3-Q4_K_M.gguf -ngl 20
```


- `-m` → specifies model path  
- `-ngl 20` → offloads layers to GPU  

![Model loading](llamacpp_ss/model_load.png)

Load time:
- ~10–15 seconds  

The model initialized successfully without errors.

---

## Inference

Test prompt:

```
Explain recursion simply
```


![CLI output](llamacpp_ss/cli_output.png)

The CLI output also displays real-time performance metrics such as tokens per second, giving better visibility into how the model is performing during inference.

Output:
- Response was generated correctly  
- Text streamed smoothly in the terminal  
- No UI involved (pure CLI interaction)  

The experience was minimal but effective.

---

## Performance Analysis

Observed performance:

- Prompt processing: ~11 tokens/sec  
- Generation speed: ~7 tokens/sec  

Observations:
- Performance was stable  
- Slightly slower perceived experience compared to LM Studio  
- Provides real-time metrics, which is useful for analysis  

---

## Observations

- Setup is more manual compared to LM Studio  
- No graphical interface (CLI-only)  
- Greater control over execution  
- Direct visibility into performance metrics  
- Requires basic familiarity with terminal usage  

---

## Overall Experience

Using llama.cpp felt more technical and closer to the core of how local AI works.

While it lacks the ease and polish of LM Studio, it provides better transparency and flexibility.

The model ran successfully on mid-range hardware (RTX 3050, 16GB RAM), showing that local inference is practical even without high-end systems.

---

## Key Takeaways

- llama.cpp is lightweight and efficient  
- Works well with GGUF quantized models  
- Provides detailed performance insights  
- Requires manual setup and CLI usage  
- Ideal for learning and experimentation  

---

## Final Thoughts

llama.cpp offers a powerful way to run AI models locally with full control over execution.

It is less beginner-friendly compared to GUI tools, but significantly more transparent and flexible.

For users interested in understanding how local inference works under the hood, llama.cpp is an excellent tool.