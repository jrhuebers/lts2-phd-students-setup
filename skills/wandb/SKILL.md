---
name: wandb
description: Set up and maintain Weights & Biases experiment tracking. Use for logging training metrics and configuration, identifying runs, resuming tracking, and offline synchronization.
---

# Weights & Biases

- Use W&B for structured experiment tracking when the project enables it; preserve existing integration and conventions.
- Load `WANDB_API_KEY` from `.env` at the repository root or `~/.env` in the user's home directory on the machine running training. Keep repository `.env` files Git-ignored; never print credentials or put them in tracked configs, logs, Git, or W&B metadata.
- Use one W&B project per research repository and the user's intended entity. Distinguish runs with names, groups, tags, and seeds; don't change project visibility automatically.
- Record `entity/project/run_id`, `run.url`, and an expressive local ID such as `method-seed17__wb-RUN_ID` alongside local outputs.
- Log every important optimization step, including composite-loss terms, validation metrics, learning rate, and timing. Record seeds, sanitized configuration and command, Git commit and dirty state when available, and scheduler metadata such as the Slurm job ID.
- When continuing an experiment, reuse its run ID with `resume="allow"` or `resume="must"`. Restore the training checkpoint separately; resuming W&B tracking does not restore model or optimizer state.
- Support offline mode (`WANDB_MODE=offline`) and later `wandb sync` when needed. Keep offline run files until synchronization succeeds.
