# Pterodactyl Arma 3 — Antistasi Ultimate

This repository will contain a custom Pterodactyl egg and supporting scripts for
an Arma 3 Linux dedicated server running Antistasi Ultimate.

## Design goals

- Preserve the official Arma 3 Launcher HTML preset workflow: upload the
  exported preset (default filename `modlist.html`) and let the Workshop
  updater resolve and update its mod IDs.
- Support both mod-provided Antistasi Ultimate mission templates and separately
  installed mission PBOs.
- Allow administrators to change missions in-game without changing startup
  variables or restarting the Arma server process.
- Preserve the Arma profile and Antistasi campaign data when changing missions.
- Provide timestamped profile backups and a restore workflow that only replaces
  profile data while the Arma process is stopped.
- Leave crash recovery to Pterodactyl Wings crash detection; an intentional
  Panel stop must remain stopped.

## Project status

Initial repository setup. The egg and scripts are not yet production-ready.
Source review and runtime validation are tracked in
[`docs/VALIDATION.md`](docs/VALIDATION.md). Do not deploy until the runtime
checklist has been completed on a test server.

## Upstream projects

- [Pterodactyl game eggs — Arma 3](https://github.com/pterodactyl/game-eggs/tree/main/arma/arma3)
- [Pterodactyl yolks — Arma 3 runtime](https://github.com/Ptero-Eggs/yolks/tree/main/games/arma3)
- [Antistasi Ultimate](https://github.com/Antistasi-Ultimate-Community/A3-Antistasi-Ultimate)

Derived upstream code will retain its original license and attribution notices.
