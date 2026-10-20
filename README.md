# BlizzardAutoMix

> **Want to use BlizzardAutoMix?** Join our [Discord server](https://discord.gg/jQMAecQzNT) and open a ticket to request access for your server.

**FACEIT-style 5v5 match management for Counter-Strike 2.**

BlizzardAutoMix takes players from warmup through captain selection, team drafting, a knife round and a live match, with in-game menus and configurable server rules.

**AutoMix By Blizzard** · Version **5.14.34** · C# / .NET 10

## Features

- Ready system with automatic match preparation when 10 human players are ready.
- Captain signup and alternating player picks. Players must be ready before becoming captains.
- Configurable deadline for each captain pick; an expired deadline returns everyone to the ready stage.
- Optional knife round with Stay / Swap side selection.
- Warmup respawning and reserve ammunition replenishment, with normal death behavior during knife and live rounds.
- Optional bots for disconnected players and empty places on an administrator force start.
- Open vacancies for human players to replace bots through the native team selection menu.
- Tactical and technical pauses, team surrender and teammate votekick voting.
- End-of-round damage reports.
- Next-map voting during round-start freeze time; the chosen map loads after the match.
- Dynamic match hostnames, with the original hostname restored after the match.
- Configurable chat prefixes, multilingual messages and an optional Discord player-request webhook.

## Match flow

1. Players join and type `.r` or `.ready` during warmup.
2. Once 10 human players are ready, the captain selection and team draft begin.
3. Captains alternate picks. Each pick has its own deadline, **60 seconds by default**.
4. If enabled, the teams play a knife round and the winning side chooses `.stay` or `.switch`.
5. The live match begins with the configured competitive rules.
6. Next-map voting opens during a qualifying round's freeze time. The map changes after the match finishes.

Administrators can also use `.start` with fewer players. Whether the remaining places receive bots is controlled by `replace_disconnected_players`.

## Requirements

- A Counter-Strike 2 dedicated server.
- [Metamod:Source](https://www.metamodsource.net/).
- [CounterStrikeSharp](https://github.com/roflmuffin/CounterStrikeSharp), minimum API version **376**, with a runtime compatible with this plugin's **.NET 10** target.
- The shared CS2MenuManager files supplied in the release package.
- Server authorization for the exact public **IP:port** used by the game server, plus outbound HTTPS connectivity for verification.

The game plugin does not require a gameplay database. The separate owner administration website uses MySQL / MariaDB.

## Installation

1. Stop the game server and back up your current configuration.
2. Extract the plugin release ZIP and copy the contents of its `game/csgo/` directory into the server's `game/csgo/` directory.
3. If upgrading, remove the previous plugin version so only one AutoMix plugin loads. Preserve and merge your existing configuration values.
4. Edit the main configuration and administrator list at the paths below.
5. Confirm that the server's public IP:port is authorized, then start the server.
6. Check the BlizzardAutoMix load result and `css_plugins list` in the server console.

```text
game/csgo/
├── addons/counterstrikesharp/
│   ├── plugins/BlizzardAutoMix/
│   ├── shared/CS2MenuManager/
│   └── configs/plugins/BlizzardAutoMix/
│       ├── admins.json
│       ├── mapchooser.json
│       └── needplayer.json
└── cfg/BlizzardAutoMix/
    ├── config.cfg
    ├── warmup.cfg
    ├── knife.cfg
    ├── live.cfg
    └── live_override.cfg
```

## Player commands

Commands accept either `.` or `/` in public or team chat. Their availability depends on the current match stage.

| Command | Purpose |
| --- | --- |
| `.r`, `.ready` | Mark yourself ready. |
| `.unready` | Cancel your ready status. |
| `.captain` | Sign up as a captain after marking yourself ready. |
| `.uncaptain` | Withdraw your captain signup. |
| `.captaininfo` | Show the current captain signup order. |
| `.menu` | Open the AutoMix action menu. |
| `.pick <player>` | Pick a player during your captain turn. |
| `.stay`, `.switch` | Choose the starting side after winning the knife round. |
| `.pause`, `.unpause` | Request a pause or resume the match. |
| `.tac`, `.tech` | Request a tactical or technical pause. |
| `.surrender` | Start a team surrender vote. |
| `.votekick` | Open the teammate votekick selection menu. |
| `.spec` | Move to spectators, subject to match rules. |
| `.needplayer` | Request players through the configured Discord webhook. |
| `.help` | Show command help. |

Use the game's team selection menu, normally **M**, to join a team. The `.ct` and `.t` shortcuts are not available.

To bind the action menu, run this once in your own CS2 console:

```text
bind "n" "css_automixmenu"
```

## Administrator commands

| Command | Purpose |
| --- | --- |
| `.start` | Force-start with the connected players; optionally fill vacancies with bots. |
| `.stop` | End the current match and return to the ready stage. |
| `.restart` | Restart the match. |
| `.roundknife` | Toggle the knife-round setting. |
| `.settings` | Display the current match settings. |
| `.map <map>` | Change the current map. |
| `.asay <message>` | Send an administrator chat message. |

Administrator access comes from the plugin's `admins.json` or CounterStrikeSharp permissions. Keep `everyone_is_admin false` for normal operation.

## Configuration

Edit `game/csgo/cfg/BlizzardAutoMix/config.cfg`. These are server configuration commands, not player chat commands.

| Setting | Default | Purpose |
| --- | --- | --- |
| `knife_enabled_default` | `true` | Enable the pre-match knife round. |
| `draft_pick_timeout` | `60` | Seconds allowed for each captain pick; minimum 1. |
| `replace_disconnected_players` | `true` | Enable disconnect replacement bots and administrator force-start bot filling. |
| `reset_to_ready_player_threshold` | `5` | Reset when the number of connected human participants reaches this value or lower. |
| `enable_damage_report` | `true` | Display end-of-round damage reports. |
| `mapchooser_enabled` | `true` | Enable next-map voting. |
| `mapchooser_trigger_score` | `11` | Open voting at the next round start after either team reaches this score. |
| `mapchooser_vote_duration` | `30` | Maximum vote duration in seconds, capped by the current freeze time. |
| `everyone_is_admin` | `false` | Grant administrator access to every player when enabled. |

Setting `replace_disconnected_players false` disables both bot replacement and bot filling on `.start`. Human spectators can still join vacant team places. Automatic ready starts still require 10 humans.

The configuration also includes chat prefixes, hostname formatting, match-start messages and playout settings. All shipped entries have English comments in [config.cfg](BlizzardAutoMix/cfg/BlizzardAutoMix/config.cfg).

## License and server authorization

The project includes upstream code under the [MIT License](BlizzardAutoMix/LICENSE). Preserve the upstream copyright and license notice when distributing that code.

CounterStrikeSharp has its own [license and MIT exception for plugins](https://github.com/roflmuffin/CounterStrikeSharp/blob/v1.0.376/LICENSE). Third-party components retain their respective license terms.

Runtime server authorization is a separate operational feature: this build verifies the server's exact IP:port before enabling the plugin. It does not replace the applicable source-code licenses.
