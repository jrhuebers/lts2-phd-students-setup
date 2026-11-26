# Set up your local agent for LTS2

First complete [SSH setup](ssh-setup.md) and verify non-interactive access.
The coding agent runs on your MacBook and submits jobs remotely; training runs
on allocated cluster resources, not on your laptop or the login node.

## 1. Install an agent and the lts2 skill

Use a local coding agent such as **Claude Code** or **Codex**, installed using
its official documentation below.

To use EPFL-hosted models, see [OpenCode and EPFL models](epfl-aiaas.md).
This is optional and independent of cluster access.

The skill uses the portable [Agent Skills format](https://agentskills.io/specification):
a directory named `lts2` containing `SKILL.md`.

Download or clone this setup repository, open a terminal in its root, and choose
your agent's **user-level skill directory**:

| Agent | Skill directory | Explicit use in chat |
| --- | --- | --- |
| [Claude Code](https://code.claude.com/docs/en/skills) | `~/.claude/skills` | `/lts2` |
| [Codex CLI / IDE](https://developers.openai.com/codex/skills/) | `~/.agents/skills` | `$lts2` (or select via `/skills`) |
| [Cursor](https://cursor.com/docs/context/skills) | `~/.cursor/skills` | Type `/` and select `lts2` |
| [OpenCode](https://opencode.ai/v2/docs/skills) | `~/.config/opencode/skills` | `@lts2` |
| [Pi](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/docs/skills.md) | `~/.pi/agent/skills` | `/skill:lts2` |

For Claude Code (change the first line if you use another agent):

```sh
skill_dir="$HOME/.claude/skills"  # Claude Code
# For Codex, use "$HOME/.agents/skills" instead.
mkdir -p "$skill_dir"
# Stop if lts2 already exists, so you don't overwrite a customized skill.
if [ -e "$skill_dir/lts2" ] || [ -L "$skill_dir/lts2" ]; then
    printf 'lts2 already exists; compare it with skills/lts2 before updating.\n'
else
    cp -R skills/lts2 "$skill_dir/lts2"
fi
```

Restart the agent, then check that `/lts2` (Claude Code), `$lts2` (Codex),
`@lts2` (OpenCode), or `/skill:lts2` (Pi) loads the skill file.
Installed copies do not update automatically; compare and copy again when this
repository changes. No agent or skill installation on the cluster is needed.

**Other agents:** use their documented skill location and copy the same `lts2`
directory there. If yours has no skill support, attach
[`skills/lts2/SKILL.md`](skills/lts2/SKILL.md) or ask it to read that local file
before working on the cluster. Don't assume every agent uses the same directory
or invocation syntax.

## 2. Prepare your research project

Open your research project on your MacBook. Tell the agent where to keep it on
the cluster, where the data and outputs belong, and how to install its dependencies.

The agent syncs code using **rsync over SSH**, sets up a cluster-side environment,
and records the remote project path in `AGENTS.md`. Uncommitted changes can be
synced; secrets, laptop environments, datasets, and outputs are excluded from
routine code transfers.

## 3. Ask for a first job

Start the agent in your research project on your MacBook. Invoke `lts2` using the
syntax above, then give it a concrete task, for example:

> Run `train.py` on LTS2 with one GPU, using this project's default config.
> Tell me when it's running and where the outputs are saved.

Replace `train.py` with your project's training entry point and specify the
experiment/config you want. The skill handles the cluster mechanics; your prompt
should describe the work you want done.

Prefer a short smoke test before a long training run. Use Slurm for computation;
don't train on the login node. **At most two GPUs per user at a time**, including
interactive allocations and array tasks; current staff QoS permits only **one
RTX PRO 5000** per user. The skill records resource types and other relevant limits.

A submitted batch job continues if you close SSH or your laptop. Your local agent
cannot keep checking it while the laptop is asleep; reconnect to inspect it.

Keep private keys, passwords, tokens, personal SSH configurations, and research
data out of this public setup repository.

Next: [check your jobs and understand cluster resources](cluster-usage.md).
