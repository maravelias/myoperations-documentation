# Automation Chapter — Local Stack Operations

This chapter complements the "DevOps" chapter by documenting the helper scripts under `local-dev/scripts/`. These scripts automate common lifecycle tasks so you can focus on application work instead of manual Docker orchestration.

## `deploy-to-vm.sh`
Purpose: Install Docker + Compose on an Ubuntu VM, configure kernel settings for SonarQube, start the stack, and optionally register a systemd unit (`myoperations-local-stack.service`).

### Usage
```bash
sudo bash local-dev/scripts/deploy-to-vm.sh [--repo-dir /opt/myoperations-devops] \
  [--user <vm-user>] [--with-systemd]
```

### Key Behaviors
- Installs prerequisites (ca-certificates, curl, gnupg, lsb-release).
- Adds Docker’s apt repo, installs Docker Engine + Compose plugin, enables the service.
- Sets `vm.max_map_count=262144` via `/etc/sysctl.d/99-sonarqube.conf`.
- Adds the specified `--user` to the `docker` group for non-root usage (requires re-login).
- Brings up the stack via `docker compose -f local-dev/docker-compose.yml up -d` in the provided repo path.
- With `--with-systemd`, writes `/etc/systemd/system/myoperations-local-stack.service` so the stack starts on boot and responds to `systemctl restart myoperations-local-stack.service`.

### When to Use
- First-time provisioning of a remote lab/VM where developers shouldn’t manually install Docker.
- Automated demos where the stack must start on boot without an operator.

### Safety Notes
- Must run as root (use sudo). Script exits if `docker-compose.yml` is missing.
- If you change the repo destination path, pass `--repo-dir` so the systemd unit references the correct directory.

## `update-stack.sh`
Purpose: Keep a cloned repository in sync with Git and restart the stack with optional destructive reset.

### Usage
```bash
bash local-dev/scripts/update-stack.sh \
  [--repo-dir <path>] [--branch main] [--remote origin] \
  [--full-reset|--restart-only] [--yes]
```

### Key Behaviors
1. Runs `git fetch` + `git pull --ff-only` against the chosen remote/branch.
2. Stops the stack via `docker compose ... down`.
3. In `--full-reset` mode (or when you confirm at the prompt), removes named volumes and the `myoperations-network` bridge.
4. Pulls updated container images and brings the stack back up detached.

### When to Use
- After another teammate pushes compose/config changes and you need them locally.
- Before demos/reviews to ensure images are pulled and config is validated.

### Safety Notes
- Full reset wipes Postgres, SonarQube, Grafana, Loki, and pgAdmin data; use when you specifically want a clean slate.
- Run from any directory using `--repo-dir`; defaults to the script’s parent repo.

## `cleanup.sh`
Purpose: Tear down the stack safely on either a local workstation or a VM where systemd manages the stack. Supports interactive confirmations and non-interactive automation.

### Usage
```bash
bash local-dev/scripts/cleanup.sh \
  [--local|--vm] [--repo-dir <path>] \
  [--remove-volumes] [--remove-network] [--purge-repo] [--yes]
```

### Key Behaviors
- Auto-detects mode: if `myoperations-local-stack.service` exists, defaults to `--vm`, otherwise `--local`.
- VM mode stops/disables the systemd unit, removes `/etc/systemd/system/myoperations-local-stack.service`, and reloads systemd.
- Runs `docker compose down` using the resolved repo path.
- Optional flags remove named volumes, the dedicated Docker network, and (VM mode only) the deployed repo directory.

### When to Use
- Cleaning a developer laptop without deleting Git history (`--local --remove-volumes --remove-network`).
- Uninstalling from a demo VM (`--vm --repo-dir /opt/myoperations-devops --purge-repo --remove-volumes --remove-network --yes`).

### Safety Notes
- With `--remove-volumes`, you’ll lose all persisted data; confirmations guard against accidental usage unless `--yes` is set.
- VM mode should be run as root; script exits early if insufficient privileges.

## Operational Playbook
1. **Provision** new VM → `deploy-to-vm.sh --with-systemd`.
2. **Day-to-day updates** on local machine → `update-stack.sh --restart-only` (default) or `--full-reset` when schema/config drift occurs.
3. **Decommission** environments → `cleanup.sh` with relevant flags.

Include this chapter after the DevOps overview in your Kroki documentation so readers can jump directly from architecture to automation workflows.
