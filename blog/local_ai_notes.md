# Running AI Locally — A Practical Guide (Phase 1 Research)

## Introduction — Why Run AI Locally?

For a long time, using powerful AI models meant relying on cloud APIs. You send a request, wait for a response, and get billed for it.

That’s changing.

Today, it’s possible to run surprisingly capable language models directly on your own machine. No internet dependency, no API costs, and full control over how things behave.

But before jumping into tools and setups, it helps to understand what’s actually happening under the hood.

---

## What Does “Running AI Locally” Mean?

At a basic level, running AI locally means:

> Using a trained model on your own machine instead of sending requests to a remote server.

You type a prompt, the model processes it locally, and generates a response — all without leaving your system.

### Inference vs Training

This distinction is important.

- **Inference** is when you use a trained model to generate outputs  
- **Training** is when you build or fine-tune a model from scratch  

When people talk about running AI locally, they almost always mean inference.

> You’re not building the model — you’re just using it.

That’s what makes this accessible.

---

## Quantization — Why This Is Even Possible

If you tried to run a full, uncompressed model locally, you’d quickly run into a wall. These models are huge.

Quantization is what makes local AI practical.

### What is Quantization?

In simple terms, quantization reduces the size of a model by lowering numerical precision.

- Example: converting 16-bit values into 4-bit  
- Result: smaller files, faster execution  

The trade-off is small:

- Slight loss in accuracy  
- Big gains in speed and usability  

A good way to think about it:

> It’s like compressing a video — you lose some detail, but it’s still perfectly usable.

---

### Common Formats You’ll See

- **GGUF**  
  The most common format for local use. Works well on CPUs and is widely supported by tools like Ollama and LM Studio.

- **GPTQ**  
  Designed for NVIDIA GPUs. Faster when you have the hardware, but a bit more setup-heavy.

- **AWQ / EXL2**  
  Newer formats focused on efficiency. Useful, but not essential for getting started.

---

## Why People Run AI Locally

This isn’t just about curiosity — there are real advantages.

### Privacy

Everything runs on your machine. No data is sent anywhere.

This matters if you’re working with:
- Personal notes  
- Sensitive documents  
- Internal tools  

---

### Cost

With cloud APIs, usage adds up quickly.

Running locally:
- No per-request cost  
- No rate limits  
- Use it as much as you want  

---

### Offline Capability

No internet required.

This is surprisingly useful in practice — especially when connectivity is unreliable.

---

### Control

You’re not locked into a single provider.

You can:
- Switch models  
- Adjust parameters  
- Experiment freely  

---

### Experimentation

Local AI is also a learning tool.

You get to:
- See how models behave  
- Compare outputs  
- Build your own workflows  

---

### Downsides (Worth Being Honest About)

It’s not perfect.

- Setup can be confusing at first  
- Performance depends on your hardware  
- Smaller models aren’t as strong as top cloud models  
- Models take up storage space  

---

## Local LLM Tools — Compared

There are several tools available, and they mostly differ in how easy they are to use and how much control they give you.

---

### 1. Ollama

This is the easiest place to start.

It handles model downloads, setup, and execution with minimal effort.

**Quick start:**
```bash
ollama run llama3
```

You can also load custom models:

```bash
ollama create my-model -f Modelfile
ollama run my-model
```

Ollama also exposes a local API, which makes it useful for building applications.

---

### 2. LM Studio

If you prefer a graphical interface, LM Studio is a great option.

It lets you:
- Browse and download models  
- Chat with them directly  
- Run a local API server  

Everything is visual, which makes it beginner-friendly.

---

### 3. llama.cpp

This is the core engine behind many local AI tools.

It’s lightweight, efficient, and gives you more control over performance.

Example (Windows):

```bash
make LLAMA_CUDA=1
./server -m model.gguf -ngl 27
```

If you want to understand what’s happening under the hood, this is where you look.

---

### 4. Hugging Face Transformers

This is the most flexible option, especially if you’re working in Python.

It allows you to:
- Load a wide range of models  
- Customize pipelines  
- Build applications  

The trade-off is complexity — it requires more setup.

---

### 5. Jan

A clean, privacy-focused desktop app.

It feels similar to ChatGPT, but everything runs locally.

---

### 6. vLLM

Built for performance.

It’s designed for high-speed inference, especially on GPUs, and is commonly used in production setups.

```bash
vllm serve meta-llama/Llama-2-7b-hf --port 8000
```

On Windows, it usually requires WSL2.

---

### 7. llamafile

The simplest option.

Download a single file, run it, and you’re done.

No setup, no dependencies.

---

## Tool Comparison

| Tool       | Best For          | UI           | Windows Support |
|------------|------------------|--------------|-----------------|
| Ollama     | General use + API | Terminal     | Yes             |
| LM Studio  | Beginners         | Desktop App  | Yes             |
| vLLM       | Production        | API only     | WSL2 required   |
| Jan        | Chat experience   | Desktop App  | Yes             |
| llama.cpp  | Developers        | CLI/WebUI    | Yes             |
| llamafile  | Zero setup        | WebUI        | Yes             |

---

## Hardware Requirements

Your hardware has a direct impact on how smooth the experience feels.

---

### CPU vs GPU

- **CPU**  
  Works everywhere, but slower  

- **GPU**  
  Much faster, but depends on available VRAM  

---

### RAM / VRAM Guidelines

| Model | RAM       | VRAM     |
|------|-----------|----------|
| 7B   | 8–16GB    | ~6GB     |
| 13B  | 16–32GB   | ~10–12GB |

---

### Example Setup

- RTX 3050 (4GB VRAM)  
- 16GB RAM  
- Windows  

This setup works well with 7B or 8B quantized models.

---

## Model Formats & Sources

Choosing the right format is just as important as choosing the model.

---

### Formats

- **GGUF** → best for local CPU usage  
- **GPTQ** → optimized for GPU  
- **Transformers (FP16/BF16)** → full precision  

---

### Where to Get Models

Most models are hosted on:
- Hugging Face Hub  
- Community contributors like TheBloke and bartowski  

---

### Example Model Name

```
llama-3-8b-instruct.Q4_K_M.gguf
```

Breakdown:

- llama-3 → model family  
- 8b → parameter size  
- instruct → tuned for chat  
- Q4 → quantization level  
- GGUF → file format  

---

## Key Takeaways

- Running AI locally means doing inference, not training  
- Quantization makes it possible on consumer hardware  
- Tools are different layers over similar underlying systems  
- Hardware determines performance  
- Starting with GGUF + 7B models is the most practical approach  

---

## What Comes Next

Now that the concepts are clear, the next step is hands-on.

Running your first model, comparing tools, and observing performance will give you a much better understanding than theory alone.

That’s where things start to get interesting.