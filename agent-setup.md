# Set up your local agent for LTS2

First complete [SSH setup](ssh-setup.md) and verify non-interactive access.
The coding agent runs on your MacBook and submits jobs remotely; training runs
on allocated cluster resources, not on your laptop or the login node.

## 1. Install an agent and the lts2 skill

Install a local coding agent of your choice using its official documentation
linked below, and sign in with an account approved for your work. You need an
agent with terminal/SSH access; a browser-only chat cannot use your MacBook's
SSH configuration.

The skill uses the portable [Agent Skills format](https://agentskills.io/specification):
a directory named `lts2` containing `SKILL.md`. Review the file before installing.

Download or clone this setup repository, open a terminal in its root, and choose
your agent's **user-level skill directory**:

| Agent | Skill directory | Explicit use in chat |
| --- | --- | --- |
| [Claude Code](https://code.claude.com/docs/en/skills) | `~/.claude/skills` | `/lts2` |
| [Codex CLI / IDE](https://developers.openai.com/codex/skills/) | `~/.agents/skills` | `$lts2` (or select via `/skills`) |
| [Cursor](https://cursor.com/docs/context/skills) | `~/.cursor/skills` | Type `/` and select `lts2` |
| [Pi](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/docs/skills.md) | `~/.pi/agent/skills` | `/skill:lts2` |

For example, for Claude Code (change the first line for your agent):

```sh
skill_dir="$HOME/.claude/skills"
mkdir -p "$skill_dir"
# Stop if lts2 already exists, so you don't overwrite a customized skill.
if [ -e "$skill_dir/lts2" ] || [ -L "$skill_dir/lts2" ]; then
    printf 'lts2 already exists; compare it with skills/lts2 before updating.\n'
else
    cp -R skills/lts2 "$skill_dir/lts2"
fi
```

Restart the agent or reload skills (Pi: `/reload`), then check that `lts2` is
listed. Installed copies do not update automatically; compare and copy again
when this repository changes. No agent or skill installation on the cluster is needed.

**Other agents:** use their documented skill location and copy the same `lts2`
directory there. If yours has no skill support, attach
[`skills/lts2/SKILL.md`](skills/lts2/SKILL.md) or ask it to read that local file
before working on the cluster. Don't assume every agent uses the same directory
or invocation syntax.

## 2. Prepare your research project

Your research repository is separate from this setup repository. Your primary
checkout can stay on your MacBook. Tell the agent:

- The **public** research repo's HTTPS URL, intended branch, and user-owned cluster
  directory. Clone it there if needed; public HTTPS clones/pulls need no GitHub
  credentials on the cluster. The agent checks repository visibility and warns you
  if it is private or visibility cannot be confirmed. It must not make a repository
  public automatically—review its contents and history before publishing.
  Never copy your laptop's private key to the cluster.
- Where the data and outputs belong, and what needs backing up. Use `scp`/`rsync`
  for data not tracked in Git; don't assume scratch storage is backed up.
- How to install dependencies in a **cluster-side** environment. Don't copy a
  MacBook virtualenv to Linux; match the framework/CUDA build to the chosen GPU.

The agent records the verified cluster clone's absolute path and the `lts2` SSH
alias in your research repo's root `AGENTS.md`, preserving existing instructions.
It commits and pushes that documentation update from your MacBook to the intended
branch, then updates the cluster clone again. It skips an already-correct entry
and reports any blocker rather than including unrelated changes or commits.

**Commit and push the changes needed for the job before submission.** The agent
checks that they are available on the intended remote branch, updates the clean
cluster clone with `git pull --ff-only`, and verifies the intended commit before
running `sbatch` through `ssh lts2`. If changes aren't pushed, the cluster checkout
has local work, histories diverge, Git access fails, or another prerequisite is
missing, it should stop and explain the blocker—not silently run old code, push
without permission, or overwrite work.

## 3. Ask for a first job

Start the agent in your research project on your MacBook. Invoke `lts2` using the
syntax above, then give it a concrete task, for example:

> Use the lts2 skill to run `train.py` on the cluster with one GPU, using this
> project's default config. Tell me when it's running and where the outputs are saved.

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
