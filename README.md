# Technical guides for LTS2 PhD students

Technical setup and usage instructions for PhD students in EPFL's LTS2 lab.

## Using the lab cluster

Use a local coding agent on your MacBook to submit research jobs to the lab's
Slurm cluster (not RCP Run:AI or SCITAS). Go through these files in order:

1. **[SSH setup](ssh-setup.md)** — configure cluster access and test `ssh lts2`.
2. **[Agent setup](agent-setup.md)** — install the `lts2` skill, prepare your
   research project's cluster clone/environment, and submit a first job.
3. **[Cluster usage](cluster-usage.md)** — check jobs with `squeue --me`, read
   logs, cancel jobs, and look up resource limits and sources.

## EPFL-hosted models

- **[OpenCode and EPFL models](epfl-aiaas.md)** — request an API key, install
  OpenCode's CLI, desktop app, and VS Code extension, and connect EPFL AIaaS.
  This is independent of the remote cluster setup.
