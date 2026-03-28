# Local LLM Tools — Compared

## 1. Ollama
Best for general use and API integration. Supports Llama 4, Mistral 3, Gemma 3.
OpenAI API compatible, supports function calling and JSON output.
Works well on Apple Silicon and AMD GPUs.

**Quick start:**
ollama run llama3

**Custom model from Hugging Face (.gguf):**
ollama create my-model -f Modelfile
ollama run my-model

## 2. LM Studio
Best for beginners — full desktop UI, no terminal needed.
Download models, chat, or launch a local OpenAI-compatible server.
Supports multiple models simultaneously, document RAG, and fine-tuning.

## 3. vLLM
Best for production/high-speed inference.
Uses PagedAttention for efficient GPU memory management.
~793 tokens/sec on Llama 70B vs Ollama's ~41.
Linux/macOS only — Windows needs WSL2 or Docker.

**Start server:**
vllm serve meta-llama/Llama-2-7b-hf --port 8000

## 4. Jan
Privacy-focused ChatGPT alternative with a clean desktop UI.
Can import models from LM Studio or GPT4All directories.
Includes local API server and optional cloud API extensions.

## 5. llama.cpp
C/C++ framework — lightweight, runs on CPU or GPU.
Powers many other tools internally (Ollama, Jan, etc).

**Build & run (Windows):**
make LLAMA_CUDA=1
./server -m model.gguf -ngl 27

## 6. llamafile
Single executable — download and run, zero setup.
Auto GPU detection, WebUI at localhost:8080.
~53 tokens/sec vs ~11 without GPU flags.

## Comparison Table

| Tool       | Best For          | UI           | Windows   |
|------------|-------------------|--------------|-----------|
| Ollama     | General + API     | Terminal     | ✅        |
| LM Studio  | Beginners         | Desktop App  | ✅        |
| vLLM       | Production        | API only     | ⚠️ WSL2   |
| Jan        | Privacy / Chat    | Desktop App  | ✅        |
| llama.cpp  | Developers        | WebUI        | ✅        |
| llamafile  | Zero setup        | WebUI        | ✅        |