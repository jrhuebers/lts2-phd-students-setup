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
   Example (non-working key): `sk-DihKfn2ReQfQFMHykE_e_g`.
   Keep the actual secret out of Git and shared docs.

## 2. Available models

Recommended models available with a **Basic** key, last checked **25 October 2026**:

| Model | Recommendation |
| --- | --- |
| `deepseek-ai/DeepSeek-V4.1-Flash-1017` | Prefer this one. |
| `zai-org/GLM-5.3-Flash` | Another good Basic-tier option. |

With a **Premium** key, you can also use `zai-org/GLM-5.3`, which might be even
better than the DeepSeek model.

## 3. Install OpenCode

We recommend **OpenCode** for using EPFL-hosted models alongside other providers.
These instructions target **OpenCode 2**; OpenCode 1 uses a different provider
configuration format.

### Terminal CLI

If you use [Homebrew](https://brew.sh/), install the CLI on your MacBook:

```sh
brew install opencode
opencode --version
```

Check that the version starts with `2.`. For other installation methods, follow
the [OpenCode 2 installation guide](https://opencode.ai/v2/docs/).

### Desktop app

Download **OpenCode Desktop** for macOS from the
[OpenCode 2 download links](https://opencode.ai/v2/docs/#desktop). Choose
**Apple silicon** for an M-series MacBook or **Intel** for an Intel Mac. Open the
`.dmg`, drag OpenCode to **Applications**, and launch it. You can open your local
research project in the desktop app instead of using the terminal interface.

### VS Code extension

In VS Code, open **Extensions**, search for **OpenCode**, and install the
[official extension (`sst-dev.opencode`)](https://marketplace.visualstudio.com/items?itemName=sst-dev.opencode).
It requires the CLI above; it is separate from the desktop app. Open your research
project in VS Code, open its integrated terminal, and run `opencode`.
On macOS, **Cmd+Esc** opens or focuses OpenCode in a split terminal.

Reference: [OpenCode IDE instructions](https://opencode.ai/docs/ide/).

## 4. Connect OpenCode to EPFL

EPFL's **external API endpoint**, `https://inference-rcp.epfl.ch/v1`, is accessible
without the EPFL network/VPN. Creating or managing your key through the portal
still requires the EPFL network/VPN.

Create the user configuration directory and open the configuration using an
editor like `nano`:

```sh
mkdir -p ~/.config/opencode
nano ~/.config/opencode/opencode.json
```

For a new file, use the configuration below. If you already have an
`opencode.json` or `opencode.jsonc`, merge the `epfl` entry into its `providers`
object instead of replacing your settings or creating a second config file.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "providers": {
    "epfl": {
      "name": "EPFL AIaaS",
      "package": "@opencode/ai/providers/openai-compatible",
      "settings": {
        "baseURL": "https://inference-rcp.epfl.ch/v1"
      },
      "models": {
        "deepseek-ai/DeepSeek-V4.1-Flash-1017": {
          "name": "DeepSeek V4.1 Flash (EPFL Basic)",
          "capabilities": {
            "tools": true,
            "input": ["text"],
            "output": ["text"]
          }
        },
        "zai-org/GLM-5.3-Flash": {
          "name": "GLM 5.3 Flash (EPFL Basic)",
          "capabilities": {
            "tools": true,
            "input": ["text"],
            "output": ["text"]
          }
        }
      }
    }
  }
}
```

Save the file, then connect the provider from a terminal:

```sh
opencode auth login epfl
```

Enter your EPFL API key at the secret prompt, not in a shell command or the JSON
file. OpenCode saves the provider account locally for its server; the local CLI
and desktop app can use it without relying on `~/.zshrc` exports. Keep OpenCode's
credential storage private and out of Git.

Open your research project in OpenCode and open the **model picker** (`/models`
in the terminal interface).

1. Toggle **EPFL AIaaS** **on** to activate the provider.
2. Toggle the **OpenCode** provider **off**.
3. Select **DeepSeek V4.1 Flash (EPFL Basic)**.

Its full model reference is `epfl/deepseek-ai/DeepSeek-V4.1-Flash-1017`.

This adds a separate **EPFL AIaaS** provider. It does **not** replace your OpenAI
or Anthropic endpoints or credentials. Connect those providers separately if you
want them too, and use the model picker to switch. No OpenCode subscription is
required to use your EPFL key.

**This setup has been confirmed working on macOS with OpenCode 2.1.4.** Start
with a small tool-use check, such as asking OpenCode to read your project's
`README.md` and summarize it, before relying on it for cluster work. If
authentication, model selection, or tool calls fail, stop and check the key's
pool, exact model ID, and endpoint rather than submitting jobs.

References: [OpenCode providers](https://opencode.ai/v2/docs/providers),
[provider accounts](https://opencode.ai/v2/docs/cli/providers), and
[configuration](https://opencode.ai/v2/docs/config).

Next: [install the lts2 skill and prepare your research project](agent-setup.md).

## 5. Optional: save your API key in your shell configuration

**Skip this for the OpenCode setup above.** This is useful for other tools that
read `EPFL_AIAAS_API_KEY`. A desktop app launched from the Dock does not normally
inherit your shell configuration.

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
