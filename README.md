# Wigud host agent — releases

This repository only hosts the **releases** of the Wigud host agent. Wigud is the fleet health monitor of CODEVS Sàrl. The source code lives in a private repository, and a workflow there publishes each release here.

The agent is a small static binary for Linux (amd64 and arm64), run by systemd. Every 60 seconds it reports to your Wigud server:

- CPU, memory and filesystem usage;
- system information (OS, kernel, uptime).

It never runs commands. It only reads `/proc` and filesystem usage, and it contains no secrets: each server gets its own token at install time.

## Install

1. In Wigud, open **Hosts → Add server** and copy the token. It is shown only once.
2. On the server, as root:

   ```sh
   curl -fsSLO https://github.com/Support-Informatique/wigud-agent/releases/latest/download/install.sh
   sudo WIGUD_SERVER_URL=https://wigud-server.example.ch WIGUD_SOURCE_TOKEN=wg_src_… sh install.sh
   ```

The installer:

- downloads the binary for this CPU and checks it against the release's `SHA256SUMS`;
- installs it to `/usr/local/bin/wigud-agent`;
- writes `/etc/wigud/agent.env` (mode 600);
- sends one test report, then enables `wigud-agent.service`.

Follow the logs with `journalctl -u wigud-agent -f`.

To install a specific version instead of the latest, add `WIGUD_AGENT_VERSION=0.1.0`.

## Update

Run `install.sh` again with the same variables. The token is in `/etc/wigud/agent.env` if you need it.

## Uninstall

```sh
sudo systemctl disable --now wigud-agent
sudo rm /etc/systemd/system/wigud-agent.service /usr/local/bin/wigud-agent
sudo rm -r /etc/wigud
sudo systemctl daemon-reload
```

Then delete the host in Wigud.

## Release files

| File | Purpose |
|---|---|
| `wigud-agent-linux-amd64` | binary for x86-64 |
| `wigud-agent-linux-arm64` | binary for ARM64 |
| `install.sh` | installer (systemd) |
| `SHA256SUMS` | checksums of the files above |
