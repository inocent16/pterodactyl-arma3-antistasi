# Validation status and test plan

This file distinguishes source review from runtime tests. No live Pterodactyl Panel, Wings, SteamCMD, or Arma server test has been performed.

## Source-reviewed candidate behavior

- The candidate egg is PTDL_v2 and retains the upstream image `ghcr.io/ptero-eggs/games:arma3`, installation script, SteamCMD update flow, HTML preset parsing, Workshop update behavior, NSS wrapper, and Tini-based runtime.
- The candidate startup removes upstream `-profiles=./serverprofile` and adds `-name=server`. Bohemia documents that `-profiles=` does not work on Linux. The exact profile/save directory remains unverified and must be observed in a running container.
- The candidate config does not hard-code the upstream example `class Missions` rotation and does not set `missionWhitelist[]`. It explicitly sets `missionsToServerRestart = 0` and `missionsToShutdown = 0`.
- Bohemia documents that in-game `#missions` selects from available missions and triggers `missionEnd`; non-zero mission-end thresholds can restart or shut down the server. An empty mission rotation allows an admin to select a mission after connecting, rather than auto-playing a hard-coded rotation.
- Antistasi Ultimate is a mod-provided mission, not a collection of mission PBOs. Its FAQ directs dedicated-server users to admin commands or a custom mission config. Separate PBOs in `MPMissions/` must be tested independently.
- Antistasi Ultimate's FAQ identifies `AntistasiUltimate.vars` or a profile-specific `.vars.Arma3Profile` as save files. Its Linux feature notes say to untick **Use New Save Method**.
- The upstream runtime stages Workshop items under `./Steam/steamapps/workshop/content/107410/<id>`, moves them into `./@<id>`, and copies `.bikey` files into `./keys/`. It does not use symlinks as active mod folders.

## Runtime tests required before production

- [ ] Import the egg into the target Pterodactyl Panel; validate JSON and variable expansion.
- [ ] Install/update with the dedicated Steam account; capture logs and confirm successful login and App ID 233780 update.
- [ ] Upload a real Arma Launcher HTML preset. Verify Workshop IDs, load order, and final `-mod` argument.
- [ ] Verify Workshop downloads, update detection, and `.bikey` handling, including duplicate key basenames.
- [ ] Confirm the effective Linux profile directory from the running process and filesystem with `-name=server`; record the exact path.
- [ ] Confirm the actual RPT and Antistasi `.vars` save locations.
- [ ] Confirm Antistasi Ultimate is selectable on each intended map.
- [ ] Confirm a separate PBO in `MPMissions/` appears in `#missions`.
- [ ] Start Antistasi with **Use New Save Method** unticked, create a campaign save, and verify the save file exists and updates.
- [ ] Switch to another mission using `#missions`, return to Antistasi, and verify the previous campaign is offered and loads correctly.
- [ ] Confirm the Arma process PID remains the same across ordinary `#missions` mission changes.
- [ ] Only after identifying the actual save directory, test manual and scheduled backups; verify archive contents and integrity.
- [ ] Restore a backup only while Arma is stopped; verify the save loads.
- [ ] Confirm Panel Stop leaves the server stopped.
- [ ] Induce a controlled crash on a test node and verify Wings restarts it. Confirm the node's crash-detection settings.
- [ ] Confirm any backup helper terminates when the container stops.

Do not mark a runtime item complete without logs or reproducible evidence.

## Primary references

- [Bohemia server config](https://community.bohemia.net/wiki/server.cfg) — mission-end thresholds and mission rotation.
- [Bohemia multiplayer server commands](https://community.bohemia.net/wiki/Multiplayer_Server_Commands) — `#missions` behavior.
- [Bohemia Linux dedicated server](https://community.bohemia.net/wiki/Arma_3%3A_Dedicated_Server) — Linux profile behavior.
- [Bohemia server profile](https://community.bohemia.net/wiki/Arma_3%3A_Server_Profile) — profile naming/location.
- [Antistasi Ultimate FAQ](https://antistasiultimate.com/FAQ/) — save files and mission delivery model.
- [Antistasi Ultimate features](https://github.com/Antistasi-Ultimate-Community/A3-Antistasi-Ultimate/wiki/Features) — Linux save-method setting.
