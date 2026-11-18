# Architecture notes

## Upstream runtime is retained

The official Arma 3 yolk's /entrypoint.sh performs SteamCMD server and
Workshop update checks, parses the Arma Launcher HTML preset, collects keys,
sets up NSS wrapper, and finally executes the expanded egg startup command.
The custom egg must retain this entrypoint and its update flow.

The current upstream flow downloads Workshop items into
./Steam/steamapps/workshop/content/107410/<id>, then moves their contents
into ./@<id>. It copies .bikey files into ./keys. Do not replace this
behavior with symlinks or a second Workshop updater unless a separately tested
reason requires it.

## Startup wrapper boundary

If a wrapper is used, it must be invoked as the egg's final expanded startup
command, after the upstream entrypoint has completed its update work. It must
forward all original Arma command-line arguments exactly and use exec for
the server process. It must not launch another SteamCMD updater or bypass the
upstream entrypoint.

## Mission switching

Use the Arma in-game #missions admin command. Keep both
missionsToServerRestart and missionsToShutdown at zero. Do not configure
missionWhitelist[] unless every intended selectable mission is explicitly
included. The admin mission selector is the chosen mechanism; ordinary
mission changes must not require a Panel restart or startup-variable edit.

Antistasi Ultimate is a mod-provided mission. A separate mission PBO, when
used, belongs in the server's MPMissions/ directory and must be verified
independently. Do not assume every Antistasi map template is automatically
present or selectable merely because the mod is downloaded.

## Profile and save data

The upstream egg currently passes -profiles=./serverprofile, but Bohemia's
Linux documentation states that -profiles is broken on Linux. Profile
location depends on the Linux server's profile name and runtime environment.
Before implementing profile backup/restore, establish the effective path from
a real container run and inspect where the .vars save files are written.

Antistasi Ultimate's Linux documentation also calls out its save-method
setting. The server must use the Linux-compatible save configuration before
we can claim campaign progress is durable.

Backups must archive the actual profile directory, not an assumed path.
Restore must refuse to run while Arma is active. Mission switching must never
delete, rename, or replace campaign files.

## Crash recovery

Crash recovery belongs to Pterodactyl Wings. Confirm the node's
system.crash_detection.enabled, detect_clean_exit_as_crash, and timeout
settings with the node administrator. The egg must not implement an infinite
restart loop. An intentional Panel Stop must remain stopped.
