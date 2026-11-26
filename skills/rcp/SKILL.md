---
name: rcp
description: Submit and manage Run:AI workloads on EPFL RCP from an agent running on the cluster's access host. Use for project selection, GPU resources, container storage, and job monitoring; not LTS2 Slurm or SCITAS.
---

# RCP

## Execution and project

- Keep work on the shared access host lightweight. Run training, substantial evaluation, and other heavy computation in Run:AI jobs; prefer training jobs for experiments.
- RCP runs Run:AI v2 but provides **Run:AI CLI v1**; use CLI v1 syntax.
- Read the Run:AI project from the research repository's root `AGENTS.md`. If unknown, ask the user before submitting and record their choice there, preserving existing instructions. Reuse it unless the user requests a change; always specify it explicitly when submitting or managing jobs.
- Record the chosen storage location and verified PVC/container mount mapping alongside the project. Don't assume another user's project, permissions, or quota applies.

## Resources

Check current project quota, GPU availability, and resource limits before submitting. Select a hardware pool when needed.

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

- Access-host storage includes the user's home directory (`~`, quota up to **100 GB**) and lab scratch at `/mnt/lts2/scratch`, `/mnt/lts2-gpeml/scratch`, and `/mnt/lts2-gempro/scratch`.
- Host paths are not automatically mounted inside jobs. Mount approved existing PVCs explicitly and use container paths for the working directory, data, and outputs.
- Use an approved image with the required dependencies; preserve its entrypoint unless the workload requires otherwise. Verify the container user can read inputs and write outputs. Never put credentials in job commands or metadata; use site-approved secret handling.

## Submission and monitoring

- Record the cluster, project, job name, and persistent output paths. Check scheduling and actual hardware with `runai describe job JOB -p PROJECT`.
- Jobs can be preempted/restarted: checkpoint regularly and support resuming. Save authoritative logs, checkpoints, and results on mounted PVC storage, not just the container filesystem or temporary log capture.
- Don't modify unfamiliar jobs, cancel jobs without approval, or delete jobs before preserving needed outputs. Don't change shared services/system configuration or expose services.

Reference: [RCP CaaS documentation](https://wiki.rcp.epfl.ch/en/home/CaaS).
