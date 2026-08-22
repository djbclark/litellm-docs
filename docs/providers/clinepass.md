# ClinePass

LiteLLM supports the models available through [ClinePass](https://cline.bot/), the Cline API. ClinePass exposes an OpenAI-compatible `/chat/completions` endpoint backed by the model catalog included in a Cline subscription (DeepSeek, Kimi, and others).

| Property | Details |
|----------|---------|
| Provider route | `clinepass/<model>` |
| Provider doc | [docs.cline.bot](https://docs.cline.bot/) |
| API endpoint | `https://api.cline.bot/api/v1` |
| Supported endpoints | `/chat/completions` |

## Authentication

Set your Cline API key as an environment variable:

```bash
export CLINEPASS_API_KEY="your-cline-api-key"
```

## Usage

```python
import os
from litellm import completion

os.environ["CLINEPASS_API_KEY"] = "your-cline-api-key"

response = completion(
    model="clinepass/deepseek-v4-flash",
    messages=[{"role": "user", "content": "Hello, how are you?"}],
)

print(response.choices[0].message.content)
```

## Usage - Streaming

```python
import os
from litellm import completion

os.environ["CLINEPASS_API_KEY"] = "your-cline-api-key"

response = completion(
    model="clinepass/deepseek-v4-flash",
    messages=[{"role": "user", "content": "Hello, how are you?"}],
    stream=True,
)

for chunk in response:
    print(chunk.choices[0].delta.content or "", end="")
```

## Usage with LiteLLM Proxy

```yaml
model_list:
  - model_name: clinepass-deepseek
    litellm_params:
      model: clinepass/deepseek-v4-flash
      api_key: os.environ/CLINEPASS_API_KEY
```

## Model Names

ClinePass requires model ids in `modelType/model` form (a bare model id is
rejected with HTTP 400). When you call `clinepass/<model>` with a bare model
name, LiteLLM automatically restores ClinePass's `cline-pass/` catalog
qualifier before sending the request:

| LiteLLM model | Sent to ClinePass |
|---------------|-------------------|
| `clinepass/deepseek-v4-flash` | `cline-pass/deepseek-v4-flash` |
| `clinepass/kimi-k3` | `cline-pass/kimi-k3` |
| `clinepass/openrouter/some-model` | `openrouter/some-model` (already qualified — forwarded unchanged) |

ClinePass does not expose a model catalog endpoint, so `litellm.get_valid_models()` returns an empty list for this provider. See the [Cline documentation](https://docs.cline.bot/) for the models included in your plan.

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `CLINEPASS_API_KEY` | Your Cline API key | Yes |
| `CLINEPASS_API_BASE` | Override the API base URL (defaults to `https://api.cline.bot/api/v1`) | No |
