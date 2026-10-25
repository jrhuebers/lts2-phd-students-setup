# Use EPFL-hosted LLMs in coding agents

## 1. Request an API key

While connected to the **EPFL network or VPN**, open the
[AIaaS API keys page](https://portal.rcp.epfl.ch/aiaas/keys) in your browser.

1. Click **New API key** and choose a name.
2. Set **Unit** to **LTS2**.
3. Set **Pool** to **Basic**. **Premium keys cost the lab money**: you must choose
   a spending limit, and Pierre must approve your request.
4. Set **Usage** to **Personal**.
5. Click **Request**. Your Basic key should be created instantly.
6. Reveal the new key's **Secret** and copy it for use in your coding agent.
   It should look like `sk-...`. Keep the actual secret out of Git and shared docs.

## 2. Choose a model

Recommended models available with a **Basic** key, last checked **25 October 2026**:

| Model | Recommendation |
| --- | --- |
| `deepseek-ai/DeepSeek-V4.1-Flash-1017` | Prefer this one. |
| `zai-org/GLM-5.3-Flash` | Another good Basic-tier option. |

With a **Premium** key, you can also use `zai-org/GLM-5.3`, which might be even
better than the DeepSeek model.

## 3. Set your key for this terminal

The examples below use the internal EPFL endpoint; stay on the EPFL network/VPN.
In macOS's default shell, **zsh**, run this and paste your key at the prompt:

```sh
read -rs 'EPFL_AIAAS_API_KEY?EPFL AIaaS API key: '; echo
export EPFL_AIAAS_API_KEY
```

This keeps the key out of the command itself and shell history. It is available
to agents launched from this terminal; repeat in a new terminal when needed.

## OpenAI Codex

With [Codex CLI](https://developers.openai.com/codex/cli/) installed, add or merge
this into `~/.codex/config.toml`. Preserve existing settings; top-level keys such
as `model` and `model_provider` go before any `[table]` headers.

```toml
model = "deepseek-ai/DeepSeek-V4.1-Flash-1017"
model_provider = "epfl"

[model_providers.epfl]
name = "EPFL AIaaS"
base_url = "https://inference.rcp.epfl.ch/v1"
env_key = "EPFL_AIAAS_API_KEY"
wire_api = "responses"
```

Start `codex` from the terminal where you set the key. To try the other Basic model:

```sh
codex --model zai-org/GLM-5.3-Flash
```

Codex requires the Responses API, not just Chat Completions. EPFL's API schema
advertises `/v1/responses`; these settings have not been tested end-to-end with
the models above. See the [Codex configuration reference](https://developers.openai.com/codex/config-reference/).

## Claude Code

With [Claude Code](https://code.claude.com/docs/en/setup) installed, run the following
in the terminal where you set the key:

```sh
export ANTHROPIC_BASE_URL="https://inference.rcp.epfl.ch"
export ANTHROPIC_AUTH_TOKEN="$EPFL_AIAAS_API_KEY"
export ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-ai/DeepSeek-V4.1-Flash-1017"
export ANTHROPIC_DEFAULT_SONNET_MODEL="$ANTHROPIC_DEFAULT_OPUS_MODEL"
export ANTHROPIC_DEFAULT_HAIKU_MODEL="$ANTHROPIC_DEFAULT_OPUS_MODEL"
export MAX_THINKING_TOKENS=0
claude --model sonnet
```

The model mappings route Claude Code's tiers to the selected EPFL model, not to
Anthropic's models. Change the model in `ANTHROPIC_DEFAULT_OPUS_MODEL` before
running the block to use another model listed above. The base URL here has **no
`/v1` suffix**. RCP's instructions require **Thinking mode to be disabled**;
`MAX_THINKING_TOKENS=0` disables Claude Code's extended-thinking request, not
necessarily the backend model's internal reasoning.

Run `/status` to check that the base URL and model are correct, then try a short
prompt. These exports apply to this terminal session; the specific models above
have not been tested end-to-end here with Claude Code.

References: RCP's [compatible tools guide](https://wiki.rcp.epfl.ch/home/AIaaS/compatible-tools#claude-code)
(EPFL network/VPN required) and [Claude Code gateway setup](https://code.claude.com/docs/en/llm-gateway-connect).
