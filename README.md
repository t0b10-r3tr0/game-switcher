# Game Switcher

A fast, lightweight game switcher for EmulationStation that lets you quickly browse and launch your recently played games without navigating through menus and game lists.

## Overview

Game Switcher displays your recently played games as full-screen screenshots that you can flip through with the D-Pad and launch instantly. It works in two modes: a **normal mode** that runs within EmulationStation, and a **cached mode** that runs before EmulationStation fully loads, allowing near-instant game switching during gameplay.

Combined with Quick Resume and Auto Save/Load, Game Switcher enables a console-like experience where you can seamlessly switch between games with minimal downtime.

## Features

- Full-screen game previews using save state screenshots, scraped images, or thumbnails
- Marquee overlay with game logos (falls back to game name text when unavailable)
- Play statistics showing play count and total play time
- Slide animation with configurable speed for navigating between games
- Cached mode for instant game switching without loading EmulationStation
- Boot to Game Switcher option to show the switcher on startup
- Remove games from the list by holding X (persists across sessions)
- Random game selection by pressing Y
- Quick Resume integration for seamless game-to-game transitions
- Pending stats tracking so play statistics stay accurate even in cached mode
- Fully configurable via the settings menu

## How It Works

### Building the Game List

Game Switcher scans all game systems (excluding collections, image viewer, and ignored platforms) for games that have been played at least once. Games are sorted by last played date, most recent first, and limited to a configurable maximum count (default: 10).

Games that have been removed by the user (via the X button) are filtered out at load time.

### Screenshot Priority

For each game, the display image is selected in this order:

1. **Auto-save screenshot** - the most recent save state screenshot
2. **Any save state screenshot** - from numbered save slots
3. **Scraped image** - downloaded game artwork
4. **Thumbnail** - box art or alternative image

### Caching

When a game is launched, Game Switcher saves a cache file (`gameswitcher_cache.json`) containing all the data needed to display the switcher without loading EmulationStation's full game database. This includes game paths, names, system names, screenshot paths, marquee paths, launch commands, and play statistics.

The cache enables **cached mode**, a lightweight version of the Game Switcher that can run before EmulationStation fully initializes. This is what makes game-to-game switching feel instant.

### Pending Stats

When a game is launched from cached mode, EmulationStation isn't fully loaded, so play statistics can't be written to the game metadata directly. Instead, Game Switcher saves **pending stats** (play count increment, elapsed time, and timestamp) to a separate file. These are applied to the actual game metadata the next time EmulationStation fully loads.

The cache file is also updated in-place so the switcher shows correct play counts and ordering even across multiple cached-mode sessions.

### Game Exclusions

Removed games are stored in `gameswitcher_excluded.json` as a JSON array of game file paths. This file is checked when loading games in both normal and cached modes. Exclusions can be cleared from the Game Switcher Settings menu, which restores all removed games.

## Activation

Game Switcher can be activated in four ways:

1. **Hotkey during gameplay** (`Function`+`Select`) - triggers via the HTTP API. If a game is running, the switcher appears after the game exits. If no game is running, it appears immediately.
2. **Boot to Game Switcher** - when enabled, shows the cached-mode switcher on startup (if no Quick Resume game is pending).
3. **Game Switcher loop** - after launching a game from cached mode, the switcher reappears when the game exits (controlled by a flag file), allowing you to chain multiple games without fully loading EmulationStation.
4. **Quick Access Menu** - when in the _Systems_ view in EmulationStation, press `select` to bring up the _Quick Access_ menu and choose _Game Switcher_.

## Controls

| Button | Action |
|--------|--------|
| Left / Right | Navigate between games |
| A | Launch the selected game |
| B | Close Game Switcher |
| Y | Jump to a random game |
| X (hold) | Remove game from the list |

A short press on X displays a brief "Hold to remove" notification.

## Settings

Game Switcher is enabled from **Main Menu > Game Settings > Enable Game Switcher**. Once enabled, a **Game Switcher Settings** submenu becomes available with the following options:

### Display

| Setting | Default | Description |
|---------|---------|-------------|
| Available Save Count | 10 | Maximum number of recently played games to show |
| Animation Speed | 500 ms | Duration of the slide transition animation |
| Enable Marquee | On | Show the game's marquee/logo image above the screenshot |
| Marquee Size | 75% | Size of the marquee as a percentage of screen width |
| Show Game Name | On | Display the game name as text when no marquee image is available |
| Enable Play Information | On | Show play count and total play time below the screenshot |
| Background Opacity | 75% | Opacity of the dark background behind play information |
| Enable Launch Animation | On | Fade out marquee and play info before launching a game |
| Show Navigation Help | On | Show button shortcuts at the bottom of the screen |

### Startup

| Setting | Default | Description |
|---------|---------|-------------|
| Boot to Game Switcher | Off | Show Game Switcher on startup when no Quick Resume game is in progress |

### Tools

| Option | Description |
|--------|-------------|
| Clear Excluded Games | Restore all removed games to Game Switcher (with confirmation dialog) |

## Recommended Setup

For the best experience, enable these features alongside Game Switcher:

- **Auto Save/Load** - automatically saves and restores your game state
- **Quick Resume** - resumes your last game instantly on boot

Together these create a seamless flow: boot into your last game, press the hotkey to switch, pick another game, and resume right where you left off.

## Data Files

All Game Switcher data files are stored in the EmulationStation configuration directory (`~/.emulationstation/`):

| File | Purpose |
|------|---------|
| `gameswitcher_cache.json` | Cached game data for fast loading |
| `gameswitcher_excluded.json` | List of removed game paths |
| `gameswitcher_pending_stats.json` | Play stats from cached-mode sessions (temporary) |
