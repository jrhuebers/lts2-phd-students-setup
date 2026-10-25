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

EPFL's **external API endpoint**, `https://inference-rcp.epfl.ch/v1`, is accessible
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

Endpoint details: [RCP technical documentation](https://wiki.rcp.epfl.ch/home/AIaaS/Technical).
