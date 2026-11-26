---
name: rcp
description: Submit and manage Run:AI workloads on EPFL RCP from an agent running on the cluster's access host. Use for project selection, GPU resources, container storage, and job monitoring; not LTS2 Slurm or SCITAS.
---

# RCP

## Execution and project

- Keep work on the shared access host lightweight. Run training, substantial evaluation, and other heavy computation in Run:AI jobs; prefer training jobs for experiments.
- The installed Run:AI CLI uses the **v1 command interface**, despite its `2.x` version number. Use `runai submit`, `runai list jobs`, `runai describe job JOB -p PROJECT`, `runai logs JOB -p PROJECT`, and `runai delete job JOB -p PROJECT`, not CLI v2 commands such as `runai training submit` or `runai workspace list`.
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
- Use an approved image with the required dependencies; preserve its entrypoint unless the workload requires otherwise. A tested A100 starting point is `nvidia/cuda:12.8.0-runtime-ubuntu24.04`, Python 3.12, and the project's locked CUDA 12.8 PyTorch wheels. Verify driver compatibility and dependency availability; this is an example, not a universal requirement or permission to change project dependencies.
- Verify the container user can read inputs and write outputs. Never put registry credentials in tool arguments or environment values; use site-approved existing Kubernetes secrets for private images.

## Runtime and launch preparation

- Pin a Python version compatible with the project's GPU dependencies. CUDA runtime images may lack Python, Git, or dependency-management tools; CUDA-enabled framework packages still need to be installed. If tools are exposed through a PVC, ensure their executables and any symlink targets are mounted and on the container's `PATH`.
- Pass numeric UID/GID. If the image has no passwd entry for that UID, set `USER`/`LOGNAME`; use writable home/cache locations and match library thread counts to the CPU allocation.
- Use a job-local environment rather than a shared checkout `.venv`. `/tmp` is ephemeral; prepared images or persistent caches can make repeated starts faster.
- For minimal runtime images, use `/tmp` as the initial container working directory, then access the mounted checkout by absolute path or change directory in the launcher. A nested mounted working directory can cause OCI startup errors before commands run. Prefer a checked-in launcher with explicit arguments over complex shell quoting; save bootstrap/dependency-setup logs on the PVC.
- Preserve existing repository checks: provide Git or a tested Git library if they require it. Avoid editing a shared checkout while a job is using it.

## Submission and monitoring

- Record the cluster, project, job name, and persistent output paths. Check scheduling and actual hardware with `runai describe job JOB -p PROJECT`.
- “Running” can mean dependency installation. Check the intended code/config/GPU, actual optimization steps, and saved checkpoints. After failures, inspect workload state and pod attempts; keep retry evidence separate. Inspect saved results before drawing conclusions.
- Jobs can be preempted/restarted: checkpoint regularly and support resuming. Save authoritative logs, checkpoints, and results on mounted PVC storage, not just the container filesystem or temporary log capture.
- Don't modify unfamiliar jobs, cancel jobs without approval, or delete jobs before preserving needed outputs. Don't change shared services/system configuration or expose services.

Reference: [RCP CaaS documentation](https://wiki.rcp.epfl.ch/en/home/CaaS).
