---
name: lts2
description: Submit and manage research jobs on EPFL's LTS2 lab Slurm cluster over SSH from a local agent. Use for LTS2 resource selection, remote training, and batch jobs; not RCP or SCITAS.
---

# LTS2

## Connection

Use `ssh lts2 '<remote command>'` from the laptop; Slurm commands run remotely.
Requires lab access and EPFL network/VPN. The user's `~/.ssh/config` should contain:

```sshconfig
Host lts2
    HostName stivm0163.xaas.epfl.ch
    User YOUR_GASPAR_USERNAME
    IdentityFile ~/.ssh/id_ed25519
```

Use the user's existing alias/key; never copy private keys or bypass SSH host-key checks.
The frontend is for editing, transfers, and scheduling—not training or heavy computation.

## Resources

Snapshot: 2026-10-20, from live `sinfo`, `scontrol`, and `sacctmgr`.
For typical deep learning, prefer **RTX PRO 5000 > TITAN RTX > GTX 1080**;
actual performance and software compatibility depend on the workload.

| GPU (best first) | VRAM per GPU | Slurm GRES type | Cluster inventory |
| --- | --- | --- | --- |
| RTX PRO 5000 | 48 GB | `rtx_pro_5000` | 3 GPUs; one each on `lts2cdk6`, `lts2cdk7`, `lts2cdk8` |
| TITAN RTX | 24 GB | `titanrtx` | 4 GPUs; three on `lts2srv4`, one on `lts2cdk9` |
| GTX 1080 | 8 GB | `gtx1080` | 4 GPUs on `lts2gdk0`; older GPU, check framework support |

VRAM is per GPU, not host RAM; the lab's RTX PRO 5000 cards are the 48 GB variant.

GPU jobs: `--partition=gpu --qos=staff --gres=gpu:TYPE:COUNT`, maximum 3 days.
CPU-only jobs: `--partition=cpu --qos=cpu`, maximum 4 days; main node `lts2srv5`
has 40 Slurm CPUs and about 125 GiB RAM (node capacity, not user allowance).

## Limits and submission

- **At most 2 GPUs per user in total at a time**, across all jobs and allocations.
  Check existing allocations before submitting; count interactive jobs and array tasks too.
  Do not evade this by switching QoS. Current `staff` QoS further limits RTX PRO 5000
  to **1 per user**, plus 24 CPUs and `50000M` host RAM total across that user's staff jobs.
  CPU QoS allows 24 CPUs and `96000M` RAM per user, no GPUs.
- Request only the CPU and memory needed, but always set the maximum permitted time
  for the selected partition/QoS (`--time=3-00:00:00` for GPU/staff,
  `--time=4-00:00:00` for CPU/cpu).
  Recheck availability with `ssh lts2 'sinfo -N -o "%N %P %c %m %G %t"'`.
  Account/QoS permissions may differ; inspect the user's association if submission is rejected.
- Expect public research repositories. Verify visibility and warn the user if the repo
  is private or visibility cannot be confirmed; never change visibility automatically.
  Use public HTTPS URLs for cluster clones/pulls so no GitHub credentials are needed there.
- Before submitting, verify that the required laptop changes are committed and pushed
  to the intended remote branch. Fetch/check both clones; in the cluster clone, verify
  the remote/branch and a clean working tree before updating with `git pull --ff-only`.
  Confirm the checkout matches the intended pushed commit. If needed changes are
  uncommitted/unpushed, access fails, history diverges, or another prerequisite is missing,
  stop and tell the user what needs fixing; don't submit stale code, auto-push research
  changes, or reset work.
- Record the verified cluster clone's absolute path and SSH alias `lts2` in the research
  repo's root `AGENTS.md` (create it if absent, preserve existing instructions).
  Commit and push this documentation update from the laptop to the intended branch,
  without including unrelated changes/commits; then fast-forward the cluster clone again.
  If the path is already correct, don't create a redundant commit. Report any push blocker.
- Use the updated cluster clone and a cluster-side environment; transfer data separately
  as needed, then submit there: `ssh lts2 'cd ~/PROJECT && sbatch job.sbatch'`.
  A typical single-GPU script requests `--nodes=1 --ntasks=1 --cpus-per-task=4 --mem=16G
  --time=3-00:00:00 --partition=gpu --qos=staff --gres=gpu:rtx_pro_5000:1` and runs
  `srun .venv/bin/python train.py` (adapt paths/environment to the project).
  Capture the job ID and remote log path; submission success does not mean the job has started.
