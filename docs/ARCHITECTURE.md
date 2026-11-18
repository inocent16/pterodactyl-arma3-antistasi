# Architecture notes

## Upstream runtime retained

The candidate egg retains the upstream Arma 3 yolk entrypoint and installation/update flow. The entrypoint performs SteamCMD server and Workshop update checks, parses the Arma Launcher HTML preset, collects keys, sets up NSS wrapper, and then runs the expanded egg startup command.

Workshop items are staged under `./Steam/steamapps/workshop/content/107410/<id>`, then moved into `./@<id>`; signing keys are copied into `./keys/`. Do not replace this with symlinks or a second Workshop updater without a separately tested reason.

## Linux profile handling

The upstream egg passes `-profiles=./serverprofile`, but Bohemia's Linux documentation says `-profiles=` does not work on Linux. The candidate instead uses `-name=server`. The actual profile/save path must be observed from a real container run before backup/restore is implemented. The upstream entrypoint's `serverprofile/rpt` tee log directory is not proof that this is the Arma profile or Antistasi save directory.

## Mission switching

The intended control is the Arma in-game `#missions` admin command. The supplied config sets both `missionsToServerRestart` and `missionsToShutdown` to zero and leaves `missionWhitelist[]` unset. It also omits the upstream sample mission rotation. Bohemia documents that an empty rotation leaves mission selection to an admin rather than starting an example mission automatically.

Antistasi Ultimate is a mod-provided mission. A separate mission PBO, when used, belongs in `MPMissions/` and must be verified independently. Do not assume every map template is automatically present or selectable just because the mod is downloaded.

## Save data

Antistasi Ultimate documentation identifies `AntistasiUltimate.vars` or a profile-specific `.vars.Arma3Profile` as save files. Its Linux feature notes require **Use New Save Method** to be unticked. Campaign persistence across `#missions` transitions is not yet proven.

No backup/restore helper is included until the real profile/save path is confirmed. Backups must target the actual save location. Restore must refuse to run while Arma is active. Mission switching must never delete, rename, or replace campaign files.

## Crash recovery

Crash recovery belongs to Pterodactyl Wings. Confirm the node's `system.crash_detection.enabled`, `detect_clean_exit_as_crash`, and `timeout` settings with the node administrator. The egg must not implement an infinite restart loop. An intentional Panel Stop must remain stopped.
