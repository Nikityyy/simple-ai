# SimpleAI

A simple, high-level API for running the Lille language model.

This package provides an easy-to-use interface for both the Hugging Face Transformers and ONNX Runtime backends, automatically downloading the required model files on first use.

## Installation

```bash
pip install simpleai-sdk
```

### A Note on ONNX Runtime and GPU Support

The package will automatically try to install the correct version of `onnxruntime` for your system:

-   If you have a **CUDA 12.x+** compatible GPU and `torch` is installed, it will automatically install `onnxruntime-gpu`.
-   If you have a **CPU-only** system, it will install `onnxruntime`.

**Important for CUDA 11.x Users:**
If you are using CUDA 11.x, `pip` cannot handle the special installation source automatically. The setup will default to the CPU version and print a warning. To get GPU acceleration, you must run the following command **after** installing `simpleai-sdk`:

```bash
pip install onnxruntime-gpu --index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/onnxruntime-cuda-11/pypi/simple/
```

## Quick Start

The `lille()` function is the main entry point. You can specify either the `"huggingface"` or `"onnx"` backend.

### Hugging Face Backend (Default)

This is the recommended backend for flexibility and ease of use, especially on GPUs.

```python
from simple_ai import lille

# This will download and cache the model on first run
# You can specify the model version, e.g., "130m-instruct" (default) or "130m-base"
model = lille("huggingface", "130m-instruct") 

# For text completion
prompt = "Artificial Intelligence is"
response = model.generate(prompt, max_new_tokens=50, temperature=0.9)
print(response)

# For chat
print("\n--- Chat Example ---")
response1 = model.chat("What is the capital of France?", max_new_tokens=50, top_k=200)
print(f"Bot: {response1}")

response2 = model.chat("And what is its population?", max_new_tokens=50, top_p=0.90)
print(f"Bot: {response2}")

# Reset the conversation history
model.reset_chat()
```

### ONNX Backend

The ONNX backend is optimized for fast CPU and GPU inference.

```python
from simple_ai import lille

# This will download and cache the model on first run
# You can specify the model version, e.g., "130m-instruct" (default) or "130m-base"
model = lille("onnx", "130m-instruct") 

# For text completion
prompt = "Artificial Intelligence is"
response = model.generate(prompt, max_new_tokens=50, temperature=0.9)
print(response)

# For chat
print("\n--- Chat Example ---")
response1 = model.chat("What is the capital of France?", max_new_tokens=50, top_k=200)
print(f"Bot: {response1}")

response2 = model.chat("And what is its population?", max_new_tokens=50, top_p=0.90)
print(f"Bot: {response2}")

# Reset the conversation history
model.reset_chat()
```

### Advanced Usage (Direct Transformers Access)

If you use the `"huggingface"` backend, you can still access the underlying `transformers` model and tokenizer for advanced use cases, just like any standard Hugging Face model.

```python
from simple_ai import lille

# Load the model and tokenizer
lille_hf = lille("huggingface")
model = lille_hf.model
tokenizer = lille_hf.tokenizer

# Example from Hugging Face docs
messages = [{"role": "user", "content": "What is gravity?"}]
input_text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)
inputs = tokenizer.encode(input_text, return_tensors="pt").to(model.device)

outputs = model.generate(inputs, max_new_tokens=50)
print(tokenizer.decode(outputs[0]))
```

---

## 🛠️ The truly open-source repos

simple-ai is a key component of my initiative to build and release a complete, truly open-source stack for language modeling. All components are designed to work together seamlessly.

*   **Tokenizer:** **[Hastings](https://github.com/Nikityyy/Hastings)** - A modern, efficient tokenizer with a 32k vocabulary.
*   **Dataset:** **[Kyoto-Corpus](https://github.com/Nikityyy/Kyoto-Corpus)** - A high-quality, small-scale dataset for instruction tuning.
*   **Model:** **[lille](https://github.com/Nikityyy/lille)** - A powerful 130-million-parameter model trained from scratch using the Hastings tokenizer.
*   **Optimizer:** **[Sophia-Triton](https://github.com/Nikityyy/Sophia-Triton)** - A memory-efficient, Triton-based implementation of the SophiaG optimizer.
*   **Evaluations:** **[simple-eval](https://github.com/Nikityyy/simple-eval)** - A straightforward framework for evaluating model performance using an LLM as a Judge.
