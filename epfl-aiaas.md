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

## 2. Available models

Recommended models available with a **Basic** key, last checked **25 October 2026**:

| Model | Recommendation |
| --- | --- |
| `deepseek-ai/DeepSeek-V4.1-Flash-1017` | Prefer this one. |
| `zai-org/GLM-5.3-Flash` | Another good Basic-tier option. |

With a **Premium** key, you can also use `zai-org/GLM-5.3`, which might be even
better than the DeepSeek model.

## 3. Save your API key in your shell configuration

The examples below use EPFL's **external API endpoint**, which is accessible
without the EPFL network/VPN. Creating or managing your key through the portal
still requires the EPFL network/VPN.

On macOS, the default shell is **zsh**. Open `~/.zshrc` using an editor like `nano`:

```sh
nano ~/.zshrc
```

Add this line, replacing the placeholder with your API key, then save the file:

```sh
export EPFL_AIAAS_API_KEY='YOUR_API_KEY'
```

Reload the file in your current terminal:

```sh
source ~/.zshrc
```

New terminal sessions will load the key automatically, making it available to
agents launched from them. Enter the secret in the editor, not as a terminal
command, to keep it out of shell history.

**Bash users:** use `~/.bashrc` and `source ~/.bashrc` instead. If you use a Bash
login shell, ensure `~/.bash_profile` sources `~/.bashrc`.

**The key is stored in plaintext.** Keep the file private and don't put the secret
in shared or version-controlled dotfiles.

## OpenAI Codex

With [Codex CLI](https://developers.openai.com/codex/cli/) installed, add or merge
this into `~/.codex/config.toml`. Preserve existing settings; top-level keys such
as `model` and `model_provider` go before any `[table]` headers.

```toml
model = "deepseek-ai/DeepSeek-V4.1-Flash-1017"
model_provider = "epfl"

[model_providers.epfl]
name = "EPFL AIaaS"
base_url = "https://inference-rcp.epfl.ch/v1"
env_key = "EPFL_AIAAS_API_KEY"
wire_api = "responses"
```

Start `codex` from a terminal that has loaded your shell configuration. To try
the other Basic model:

```sh
codex --model zai-org/GLM-5.3-Flash
```

Codex requires the Responses API, not just Chat Completions. EPFL's internal API
schema advertises `/v1/responses`, but the **external endpoint filters API routes
and HTTP methods**. Codex compatibility through the external endpoint and with
the models above has not been tested end-to-end. If the external endpoint rejects
the required route/method, connect to the EPFL network/VPN and change `base_url`
to `https://inference.rcp.epfl.ch/v1`.

References: [RCP endpoint documentation](https://wiki.rcp.epfl.ch/home/AIaaS/Technical)
and [Codex configuration reference](https://developers.openai.com/codex/config-reference/).
