
# LeanChain

LeanChain is a lightweight and modular LLM orchestration framework inspired by LangChain — but designed for clarity, runtime flexibility, and minimal overhead. It supports multiple LLM vendors, streaming, memory, tools, and caching with future extensibility to multimodal tasks.

---

## 🏗️ Architecture Overview

LeanChain is built on 3 core principles:

1. **Provider Abstraction**: Unified interfaces for different AI vendors (OpenAI, Azure, etc.)
2. **Modular Composition**: Each capability (chat, embedding, image, audio) is isolated
3. **Runtime Flexibility**: Switch vendors or models during runtime using `load_provider`

### 📐 Components

| Module               | Purpose                                              |
|----------------------|------------------------------------------------------|
| `llm_provider.py`    | Defines `ChatProvider`, `EmbeddingProvider`, etc.    |
| `openai_provider.py` | Implements OpenAI via new `openai.OpenAI()` client   |
| `azure_openai_provider.py` | Adds support for Azure OpenAI SDK            |
| `core_service.py`    | Main wrapper that routes chat, tools, memory, cache  |
| `ai_service_hub.py`  | Combines multiple providers into one unified service |
| `provider_loader.py` | Loads the correct provider based on config/env       |
| `memory.py`          | Defines memory modules (e.g., `ShortMemoryBuffer`)   |
| `tool.py`            | Defines external tools like `MathTool`               |
| `cache.py`           | Simple file-based caching layer                      |

---

## ⚙️ Installation

Clone or unzip this repository and install with:

```bash
pip install -e .
```

Make sure to install OpenAI Python SDK (already in `setup.py`):

```bash
pip install openai
```

---

## 🧠 Usage Example

### 1. Set environment variables

For OpenAI:
```bash
export LLM_VENDOR=openai
export OPENAI_API_KEY=your-key-here
export OPENAI_MODEL=gpt-4o-mini
```

---

### 2. Use in Python with simple chat

```python
from leanchain import provider_loader
from leanchain.core_service import LLMCoreService
from myconfig import AppConfig  # your own wrapper for env

config = AppConfig()
provider = provider_loader.load_provider(config)
llm = LLMCoreService(provider)

response = llm.chat("What's the weather like on Mars?")
print(response)
```

---

### 3. Use in Python with Memory, Tools, and Cache

```python
from leanchain.provider_loader import load_provider
from leanchain.core_service import LLMCoreService
from leanchain.memory import ShortMemoryBuffer
from leanchain.tool import MathTool
from leanchain.cache import Cache
from myconfig import AppConfig

config = AppConfig()
provider = load_provider(config)

llm = LLMCoreService(
    provider=provider,
    memory=ShortMemoryBuffer(max_turns=3),
    tools=[MathTool()],
    cache=Cache(".leanchain_cache")
)

# Simulate conversation
chat_history = []
user_input = "What's 3 + 5?"
response = llm.chat(user_input, chat_history)
print("🤖", response)

chat_history.append({"role": "user", "content": user_input})
chat_history.append({"role": "assistant", "content": response})
```

---

## 🔁 Switching Vendors or Models at Runtime

```python
llm.set_provider(load_provider(config, vendor="azure", model="gpt-4-code"))
```

---

## 🔮 What’s Next

- ✅ Memory support
- ✅ Tool plugins (e.g., calculator)
- ✅ File-based cache
- 🔜 Agents and knowledge graphs
- 🔜 Multimodal tasks (image/audio)

---

## 📄 License

MIT License – free to use, modify, and extend.
