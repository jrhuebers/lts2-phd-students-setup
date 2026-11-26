---
name: rcp
description: Submit and manage Run:AI workloads on EPFL RCP from an agent running on the cluster's access host. Use for project selection, GPU resources, container storage, and job monitoring; not LTS2 Slurm or SCITAS.
---

# RCP

## Execution and project

- Keep work on the shared access host lightweight. Run training, substantial evaluation, and other heavy computation in Run:AI jobs; prefer training jobs for experiments.
- RCP runs Run:AI v2 but provides **Run:AI CLI v1**. Use the installed CLI's syntax and `--help`, not CLI v2 examples.
- Read the Run:AI project from the research repository's root `AGENTS.md`. If unknown, ask the user before submitting and record their choice there, preserving existing instructions. Reuse it unless the user requests a change; always specify it explicitly when submitting or managing jobs.
- Record the chosen storage location and verified PVC/container mount mapping alongside the project. Don't assume another user's project, permissions, or quota applies.

## Resources

Check current project quota, GPU availability, and resource limits before submitting. Request only the CPU, RAM, and GPUs needed; choose a pool explicitly when hardware matters.

Pools listed in the RCP profile (verify current inventory):

| Pool | Hardware |
| --- | --- |
| `default` | NVIDIA A100 SXM4 80 GB |
| `a100-40g` | NVIDIA A100 SXM4 40 GB |
| `h100` | NVIDIA H100 80 GB |
| `v100` | NVIDIA V100 SXM2 32 GB |
| `h200` | NVIDIA H200 |
| `b300` | NVIDIA B300 SXM6 |

## Storage and containers

- Access-host storage includes the user's home directory (`~`, quota up to **100 GB**) and lab scratch at `/mnt/lts2/scratch`, `/mnt/lts2-gpeml/scratch`, and `/mnt/lts2-gempro/scratch`. Verify the user's access; don't assume scratch is backed up.
- Host paths are not automatically mounted inside jobs. Identify approved existing PVCs, mount them explicitly, and use container paths for the working directory, data, and outputs. Don't infer PVC names from host paths; ask if the mapping is unclear.
- Use an approved image with the required dependencies; preserve its entrypoint unless the workload requires otherwise. Verify the container user can read inputs and write outputs. Never put credentials in job commands or metadata; use site-approved secret handling.

## Submission and monitoring

- Verify project, image, resources, mounts, and launch command before submission. Stop and explain missing prerequisites rather than guessing.
- Record the cluster, project, job name, and persistent output paths. Check scheduling and actual hardware with `runai describe job JOB -p PROJECT`; submission alone does not mean the workload is running.
- Jobs can be preempted/restarted: checkpoint regularly and support resuming. Save authoritative logs, checkpoints, and results on mounted PVC storage, not just the container filesystem or temporary log capture.
- Don't modify unfamiliar jobs, cancel jobs without approval, or delete jobs before preserving needed outputs. Don't change shared services/system configuration or expose services without approval.

Reference: [RCP CaaS documentation](https://wiki.rcp.epfl.ch/en/home/CaaS).
