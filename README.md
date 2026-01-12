# Dodge and Dash (Ruby + Gosu)

A 2D top‑down lane‑dodging racer inspired by classic web games from around 2010. Your goal is simple: weave through traffic for as long as possible and chase a new high score. Choose between one‑way or two‑way traffic, tweak difficulty, pick your car, and compete against your saved top scores.

## Features
- One‑way and two‑way traffic game modes
- Four difficulties: Easy, Normal, Hard, Very Hard
- Three selectable cars (Garage)
- Persistent Top 5 High Scores
- Language support: English (`en`) and Vietnamese (`vi`)
- Optional pre‑race countdown and toggleable sound/music
- In‑game speedometer and smooth car animations

## Tech Stack
- Ruby
- [Gosu](https://www.libgosu.org/) for windowing, input, audio, and rendering
- JSON for save data and localization

## Controls
- Navigation (menus)
  - Arrow Up/Down: Move selection
  - Enter: Select
  - Esc: Back/Close
- Gameplay
  - Left/Right: Steer
  - Up: Accelerate
  - Down: Brake
  - Esc: Pause/Resume
  - While paused: Up/Down to navigate; Enter to choose
- Gamepad (Gosu mappings)
  - D‑pad Left/Right: Steer
  - Button 2: Select / Accelerate
  - Button 3: Brake
  - Button 1: Back / Pause

## Setup
1) Install Ruby (3.x recommended).
2) Install the Gosu gem and its native dependencies.

Windows:
```powershell
# Using RubyInstaller (with MSYS2) is recommended
gem install gosu
```

macOS (Homebrew):
```bash
brew install sdl2 sdl2_image sdl2_mixer sdl2_ttf
gem install gosu
```

Ubuntu/Debian:
```bash
sudo apt update
sudo apt install -y libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev
gem install gosu
```

If you run into issues installing Gosu, see the getting started guides:
- Windows: `https://github.com/gosu/gosu/wiki/Getting-Started-on-Windows`
- macOS: `https://github.com/gosu/gosu/wiki/Getting-Started-on-OS-X`
- Linux: `https://github.com/gosu/gosu/wiki/Getting-Started-on-Linux`

## Run
From the project root:
```bash
ruby game.rb
```

The game window is 512×512 by default.

## Game Modes
- One‑Way: Traffic flows in your direction; tighter lane bounds.
- Two‑Way: Both directions are active; increased challenge and lane variety.

You can switch modes from Menu → Scenario (Last selection is remembered).

## Options
- Language: English or Vietnamese (cycles through available `langs/*.json`)
- Sound: On/Off (toggles music and SFX)
- Difficulty: Easy, Normal, Hard, Very Hard
  - Internally controls car spawn waves, lateral movement, and score factor
- Countdown: On/Off (pre‑race “3‑2‑1‑GO!” overlay)

## High Scores
Top 5 scores are persisted between sessions and can be cleared from the High Scores screen.

## Garage
Pick your preferred vehicle (three choices, animated). Your selection persists.

## Data & Configuration
Persistent data lives in `data/data.json`. Key fields:
```json
{
  "high_scores": [11, 4, 0, 0, 0],
  "last_scenario": 0,
  "current_car": 0,
  "config": {
    "sound": true,
    "language": "en",
    "current_difficulty": "easy",
    "countdown": true,
    "difficulty": {
      "easy":      { "cars_wave": [1, 1], "cars_move": false, "score_factor": 225 },
      "normal":    { "cars_wave": [1, 2], "cars_move": false, "score_factor": 150 },
      "hard":      { "cars_wave": [1, 2], "cars_move": true,  "score_factor": 50  },
      "very_hard": { "cars_wave": [2, 2], "cars_move": true,  "score_factor": 25  }
    }
  }
}
```
- `last_scenario`: 0 = One‑Way, 1 = Two‑Way
- `current_car`: 0..2
- `current_difficulty`: one of the defined keys
- `cars_wave`: spawns per interval, indexed by mode (one‑way/two‑way)
- `cars_move`: allows lateral movement for AI cars at higher difficulties
- `score_factor`: lower values increase scoring speed

The file is automatically updated on exit.

## Localization
Language data is under `langs/` (`en.json`, `vi.json`). To add a language:
1) Copy an existing file (e.g., `en.json`) to a new file like `es.json`.
2) Translate string values.
3) Set `"language"` to the display name.
4) Start the game and cycle Language in Options, or set `"language"` in `data.json`.

Required keys include:
- `menu`, `game_over`, `new_score`, `score_label`, `option_back`, `option_clear`,
  `high_scores`, `cars_option`, `options`, `options_sound`, `options_countdown`,
  `pause_options`, difficulty labels (`easy`, `normal`, `hard`, `very_hard`),
  and `countdown`.

## Project Structure
```
.
├─ game.rb                 # Entry point (Gosu window, state switching, audio control)
├─ models/
│  ├─ state.rb             # Base state interface
│  ├─ player.rb            # Player car, movement, collision
│  ├─ car.rb               # AI cars, movement patterns, audio
│  ├─ road.rb              # Scrolling background
│  └─ speedometer.rb       # UI speedometer
├─ states/
│  ├─ game_state.rb        # Core game loop, scoring, spawning, pause/death/loading
│  ├─ menu_state.rb        # Base menu behavior
│  ├─ game_states/
│  │  ├─ oneway.rb         # One-way mode config/logic
│  │  └─ twoway.rb         # Two-way mode config/logic
│  └─ menu_states/
│     ├─ main.rb           # Main menu
│     ├─ scenario.rb       # Scenario selection
│     ├─ highscores.rb     # High scores
│     ├─ garage.rb         # Car selection
│     └─ options.rb        # Settings (language, sound, difficulty, countdown)
├─ media/                  # Images, fonts, sounds
├─ langs/                  # Localization JSON
└─ data/data.json          # Save and config
```

## Assets
All fonts, images, and sounds are loaded from `media/`. Replace or extend them to customize the look and feel. Ensure file names and paths match those referenced in code.

## Troubleshooting
- Gosu install errors on Windows:
  - Use RubyInstaller with MSYS2; run the MSYS2 dev tools setup when prompted.
  - Re‑run: `gem install gosu`.
- Missing libraries on macOS/Linux:
  - Install SDL2 dependencies (see setup above) and then `gem install gosu`.
- Black window or missing assets:
  - Run from the project root so relative paths resolve correctly.
- No sound:
  - Toggle Sound in Options; confirm OS audio and device volumes.
- Controller not working:
  - Ensure your controller is recognized by the OS; Gosu uses standard mappings.