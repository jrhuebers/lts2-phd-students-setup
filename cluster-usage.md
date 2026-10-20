# Check jobs and cluster resources

After [SSH setup](ssh-setup.md) and [agent setup](agent-setup.md), use these
commands to check jobs independently of your agent. This guide is for the LTS2
lab's Slurm cluster, not RCP Run:AI or SCITAS.

## See your jobs

From your MacBook:

```sh
ssh lts2 'squeue --me'
```

Or log in with `ssh lts2`, then run **`squeue --me`** on the cluster. It lists your
pending, running, and completing jobs—not just the running ones.

- `R`: running (resources allocated).
- `PD`: pending; `NODELIST(REASON)` explains why, e.g. `Resources` or `Priority`.
- `CG`: completing and releasing resources.

Useful variations, all run from your MacBook:

```sh
ssh lts2 'squeue --me --states=RUNNING'  # only running jobs
ssh lts2 'sacct -S today --format=JobID,JobName,State,Elapsed,ExitCode'
```

Finished jobs leave `squeue`; use `sacct` and the job's logs to check success.
By default, `sbatch` writes stdout/stderr to `slurm-JOBID.out` in the submission
directory unless the script specifies another path. To cancel one of your jobs,
replace `JOBID` in `ssh lts2 'scancel JOBID'`.

## Open a shell inside a running job

First log in with `ssh lts2`. On the cluster, use `squeue --me` to find one of
**your own running jobs**, then replace `JOBID` below with its job ID:

```sh
srun --jobid=JOBID --overlap --pty bash -l
```

For a single-node, single-task job, this starts an additional shell in the existing
allocation; it does not request a new job or attach to the training process itself.
`--overlap` lets this step share resources with the running training step,
rather than waiting for those resources to be released.

Inside that shell, you can run `nvidia-smi` (for a GPU job), navigate to your
output directory, and inspect generated artifacts. Use `exit` or Ctrl-D to leave
this extra shell; the original batch job keeps running. The shell may need its
own environment activation—it isn't automatically the training process's environment.

For a one-off GPU check, you can also run this directly from your MacBook:

```sh
ssh lts2 'srun --jobid=JOBID --overlap nvidia-smi'
```

These commands use resources already allocated to the job, so keep inspection
lightweight. For a multi-node job, add `--nodelist=NODE` to select a particular
allocated node. Shared files such as logs and checkpoints can usually be inspected
from the login node without joining the job; the extra shell is useful for GPU
checks or node-local files. Once the job finishes, its allocation is no longer
available to join.

## Resource facts and sources

Resource inventory and limits in the [`lts2` skill](skills/lts2/SKILL.md) were
checked **2026-10-20** against live Slurm metadata and the readable cluster
configuration. Configured inventory is not a promise of availability. GPU ranking
is a practical deep-learning preference, not a measured benchmark. RTX PRO 5000
is the newest tier, TITAN RTX is the middle tier, and GTX 1080 is a legacy fallback;
match your framework/CUDA build to the selected GPU.

**At most two GPUs per user at a time, across all jobs**, including interactive
allocations and array tasks. Current staff QoS permits only **one RTX PRO 5000**
per user. See the skill for the other relevant limits.

To recheck on the cluster:

```sh
sinfo -N -o '%N %P %c %m %G %t'
scontrol show partition
sacctmgr show assoc where user="$USER" format=User,Partition,QOS,DefaultQOS
sacctmgr show qos format=Name,MaxTRESPU,MaxWall
```

`%m` reports node host RAM in MiB, **not GPU VRAM or your personal allowance**.
The default partition is `cpu`; GPU jobs must explicitly select `gpu`. The CPU
partition also contains `lts2pc3`, but it was down/drained at verification time.
For exact GPU model/VRAM, run `nvidia-smi` inside your allocated GPU job.

References: Slurm's [batch submission](https://slurm.schedmd.com/sbatch.html),
[GPU requests](https://slurm.schedmd.com/gres.html), and
[queue commands](https://slurm.schedmd.com/squeue.html), and
[job steps / srun](https://slurm.schedmd.com/srun.html), plus NVIDIA's
[RTX PRO 5000 overview](https://www.nvidia.com/en-us/products/workstations/professional-desktop-gpus/rtx-pro-5000/).
A lab-specific cluster handbook was not located during verification; ask the lab
administrator for the current documentation and any additional policies.
Account permissions and administrator guidance take precedence over this snapshot.
