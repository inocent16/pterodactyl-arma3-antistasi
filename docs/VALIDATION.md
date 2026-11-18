# Validation status and test plan

This file distinguishes documentation review from runtime tests. No live
Pterodactyl Panel, Wings, SteamCMD, or Arma server test has been performed yet.

## Confirmed from upstream source

- The official Pterodactyl Arma 3 egg is PTDL_v2 and uses
  ghcr.io/ptero-eggs/games:arma3.
- Its startup command invokes the server binary with -profiles=./serverprofile,
  -cfg=basic.cfg, -config=server.cfg, -mod, and -serverMod.
- Its MOD_FILE variable accepts an Arma 3 Launcher-exported HTML file. The
  upstream Arma 3 runtime checks for the "Created by Arma 3 Launcher" marker,
  extracts Workshop IDs, and constructs @<id> mod folder names.
- The upstream runtime uses Workshop game App ID 107410 and SteamCMD
  workshop_download_item. It moves downloaded content into @<id> folders
  and copies .bikey files into keys/. It does not use the Workshop download
  tree as the active mod directory.
- The upstream runtime performs mod update checks before executing the egg's
  expanded startup command. Any custom wrapper must be the final startup
  command and must not bypass the upstream entrypoint.
- The upstream runtime image is based on Debian and starts through Tini and
  /entrypoint.sh.

## Confirmed from Bohemia / Antistasi documentation

- In-game #missions opens a list of available missions. It triggers missionEnd;
  missionsToServerRestart and missionsToShutdown can cause a process restart or
  shutdown if configured with a non-zero threshold.
- The documented default for both mission-end thresholds is zero. The custom
  config must explicitly keep both at zero.
- Mission selection includes available missions, or only entries allowed by
  missionWhitelist[] when that whitelist is configured.
- Antistasi Ultimate is distributed as a mod, not as a collection of mission
  PBOs. Its FAQ says to use admin commands or a custom mission config to switch
  to the mission. Separately uploaded PBO missions are a different mechanism.
- Antistasi Ultimate's FAQ identifies AntistasiUltimate.vars or a
  profile-specific .vars.Arma3Profile as save files. Its Linux feature notes
  say the reimplemented save options allow Linux servers to save correctly and
  that "Use New Save Method" should be unticked.
- The Antistasi Ultimate import/export GUI applies only to saves persistently
  saved on version 12.2.0 or later.
- Bohemia's Linux dedicated-server documentation warns that -profiles= does
  not work on Linux. The exact effective profile path must therefore be
  established for this container image and process before implementing
  automated save backups. Do not assume serverprofile/ is the live profile
  just because the upstream egg passes -profiles=./serverprofile.
- Pterodactyl Wings has node-level crash detection. It can restart a server
  that exits without a Panel Stop action; detect_clean_exit_as_crash controls
  whether clean exit code 0 is also treated as a crash. The timeout limits
  repeated rapid crash restarts. This is a node setting, not an egg feature.

## Required runtime tests

- [ ] Import the egg into a current Pterodactyl Panel.
- [ ] Install/update the server using the dedicated Steam account; capture
      SteamCMD logs and confirm successful login and App ID 233780 update.
- [ ] Upload an actual Arma Launcher HTML preset. Verify all Workshop IDs,
      expected load order, and the final -mod argument.
- [ ] Verify Workshop downloads, update detection, and .bikey handling,
      including two mods that contain a key with the same basename.
- [ ] Confirm the effective Linux profile directory from the running process
      and filesystem; record the exact -name and environment used.
- [ ] Confirm Antistasi Ultimate's mission is selectable on each intended map.
- [ ] Confirm a separate PBO in MPMissions/ appears in #missions.
- [ ] Start Antistasi, create and persist a campaign save, switch to another
      mission using #missions, then return and verify the old save is offered.
- [ ] Confirm the Arma process PID remains the same across ordinary #missions
      mission changes.
- [ ] Verify Antistasi Ultimate's Linux-compatible save setting is configured
      as its documentation requires.
- [ ] Verify manual and scheduled profile backups while the server is active.
      Confirm archives contain the actual save file and pass integrity checks.
- [ ] Restore a backup only while the server is stopped; verify the save loads.
- [ ] Confirm Panel Stop leaves the server stopped.
- [ ] Induce a controlled crash on a test node and verify Wings restarts it.
- [ ] Verify backup helper processes terminate when the container stops.

Do not mark a runtime item complete without logs or reproducible evidence.
