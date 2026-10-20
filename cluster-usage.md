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
[queue commands](https://slurm.schedmd.com/squeue.html), plus NVIDIA's
[RTX PRO 5000 overview](https://www.nvidia.com/en-us/products/workstations/professional-desktop-gpus/rtx-pro-5000/).
A lab-specific cluster handbook was not located during verification; ask the lab
administrator for the current documentation and any additional policies.
Account permissions and administrator guidance take precedence over this snapshot.
