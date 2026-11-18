# Pterodactyl Arma 3 — Antistasi Ultimate

A custom Pterodactyl egg candidate and configuration for an Arma 3 Linux dedicated server running Antistasi Ultimate.

## Included

- `egg/egg-arma3-antistasi-ultimate.json`: PTDL_v2 egg based on the upstream Pterodactyl Arma 3 egg.
- `egg/config/basic.cfg` and `egg/config/server.cfg`: network and server configuration templates.
- The official Arma 3 Launcher HTML preset workflow is retained: upload the exported preset (default filename `modlist.html`) and let the upstream Workshop updater resolve and update its mod IDs.
- Mission selection is intended to use the in-game `#missions` admin command. The server config explicitly sets `missionsToServerRestart = 0` and `missionsToShutdown = 0`, leaves `missionWhitelist[]` unset, and omits the upstream example mission rotation.
- The upstream installation and Workshop update flow is retained; Workshop items are staged by SteamCMD and then moved into `@<WorkshopID>` folders with signing keys copied into `keys/`.

## Important limitations

**This is a source-reviewed candidate, not a runtime-validated production egg.** No live Pterodactyl Panel/Wings/Arma test has been performed. In particular, mission switching without PID change, Antistasi campaign persistence across mission changes, and backup/restore have not been demonstrated.

Bohemia documents that `-profiles=` does not work on Linux. This egg removes that parameter and sets `-name=server`; the effective profile/save directory must still be confirmed from a real container run before any automated backup tooling is enabled. Do not assume `/home/container/serverprofile` is the Antistasi save directory.

Antistasi Ultimate's own documentation says to untick **Use New Save Method** for Linux servers. Confirm this in the mission setup before starting a campaign.

## Installation outline

1. Import `egg/egg-arma3-antistasi-ultimate.json` into Pterodactyl.
2. Configure the dedicated Steam account credentials required by the upstream installer. Keep these credentials private; Steam authentication behavior must be tested with your account.
3. Install the server and upload the HTML preset exported by Arma 3 Launcher to the server root as `modlist.html` (or set `MOD_FILE` to the uploaded filename).
4. Ensure the Antistasi Ultimate Workshop item and every required client mod/map are in the preset.
5. Set a strong admin password in `server.cfg` before exposing the server. The template intentionally leaves `passwordAdmin` commented.
6. Test `#missions` with an admin account and confirm the Arma PID remains unchanged during a mission transition.
7. Test Antistasi save creation, mission switching, and save reload before relying on campaign persistence.

## Backups

Automated backup/restore scripts are deliberately not included yet. Bohemia's Linux profile-path documentation and Antistasi's save-file documentation require a live-path check first. A backup helper that targets the wrong directory would create false confidence and could miss or overwrite campaign saves. See [the validation checklist](docs/VALIDATION.md).

## Upstream projects

- [Pterodactyl game eggs — Arma 3](https://github.com/pterodactyl/game-eggs/tree/main/arma/arma3)
- [Pterodactyl yolks — Arma 3 runtime](https://github.com/Ptero-Eggs/yolks/tree/main/games/arma3)
- [Antistasi Ultimate](https://github.com/Antistasi-Ultimate-Community/A3-Antistasi-Ultimate)
- [Bohemia: server config](https://community.bohemia.net/wiki/server.cfg)
- [Bohemia: Linux dedicated server](https://community.bohemia.net/wiki/Arma_3%3A_Dedicated_Server)
- [Antistasi Ultimate FAQ](https://antistasiultimate.com/FAQ/)

The egg retains upstream installation/runtime logic and attribution. Review upstream licensing before redistribution.
