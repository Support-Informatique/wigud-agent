# Wigud host agent

Releases of the Wigud host agent. It reports a server's or PC's health to your Wigud server every 60 seconds. The source lives in a private repository; this file is published from there with each release.

Get the token in Wigud: **Hosts → Add server** (shown once), or the key button on the host page for a new one. Wigud also shows these commands with your URL and token filled in.

## Linux (systemd, amd64 or arm64)

**Install**

```sh
curl -fsSLO https://github.com/Support-Informatique/wigud-agent/releases/latest/download/install.sh && sudo WIGUD_SERVER_URL=https://your-wigud-server WIGUD_SOURCE_TOKEN=wg_src_… sh install.sh
```

Add `WIGUD_DOCKER=1` before `sh` to also monitor Docker (it gives the agent the `docker` group, which is root-equivalent).

**Update**

```sh
curl -fsSLO https://github.com/Support-Informatique/wigud-agent/releases/latest/download/install.sh && sudo sh install.sh
```

It keeps the URL, token and Docker choice. Check it: `journalctl -u wigud-agent -n 20`.

**Uninstall**

```sh
sudo systemctl disable --now wigud-agent && sudo rm -rf /etc/systemd/system/wigud-agent.service /usr/local/bin/wigud-agent /etc/wigud /var/lib/wigud-agent && sudo systemctl daemon-reload && sudo userdel wigud-agent
```

## Windows (x64 or ARM64)

Run in **PowerShell as Administrator**.

**Install**

```powershell
Invoke-WebRequest -UseBasicParsing 'https://github.com/Support-Informatique/wigud-agent/releases/latest/download/install.ps1' -OutFile install.ps1; Unblock-File .\install.ps1; powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\install.ps1 -ServerUrl 'https://your-wigud-server' -SourceToken 'wg_src_…'
```

**Update**

```powershell
Invoke-WebRequest -UseBasicParsing 'https://github.com/Support-Informatique/wigud-agent/releases/latest/download/install.ps1' -OutFile install.ps1; Unblock-File .\install.ps1; powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\install.ps1
```

It keeps the URL and token. Logs: **Event Viewer → Windows Logs → Application**, source **WigudAgent**.

**Uninstall**

```powershell
Stop-Service WigudAgent; sc.exe delete WigudAgent; Remove-Item -Recurse -Force "$env:ProgramFiles\Wigud Agent", "$env:ProgramData\Wigud Agent"
```

After uninstalling, delete the host in Wigud.

## Notes

- Both installers check the download against `SHA256SUMS` and send a test report before starting the service; if it fails, nothing changes.
- To use a specific version: Linux `WIGUD_AGENT_VERSION=0.5.0`, Windows `-Version 0.5.0`.
- New token (after **New agent token** in Wigud): run the install command again with the new token.
- The agent only reads (system facts, logs, the Docker API). Wigud cannot send it commands.
