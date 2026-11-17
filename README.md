# Tetris in ibyt

A complete Tetris implementation for the terminal, written in ibyt

## What it is

Tetris runs in any ANSI terminal. The playfield is drawn with colored
blocks (two characters per cell) using `ConsoleGfx`, the falling pieces
are moved and rotated with per-axis collision detection, and sound is
played through the PC speaker via the `Sound` library.

## Features

- **10 × 20 playfield** with 7 tetrominoes (I, J, L, O, S, T, Z)
- **Wall kicks**: rotation tries 5 positions, like modern Tetris
- **Levels**: every 10 lines the drop speed increases
- **Scoring**: 100 / 300 / 500 / 800 points per 1 / 2 / 3 / 4 lines,
  multiplied by the current level
- **Best score** kept during the session
- **Pause** (`p`), **quit** (`q` / `ESC`), **new game** (`r`)
- **Sound**: lock, line clear, tetris, game over – short PC-speaker melodies
- **Scalable cells**: on start, the largest cell size (3, 2 or 1) is chosen
  that fits the terminal
- **Flicker-free rendering** via `ConsoleGfx.ShadowOn` / `Present`
- **No field table**: collisions and full rows are read back from the
  screen with `GetPoint` – the screen *is* the game state

## Controls

| Key | Action |
|---|---|
| `←` / `A` | move left |
| `→` / `D` | move right |
| `↑` / `W` | rotate |
| `↓` / `S` | soft drop |
| `Space` | hard drop |
| `p` | pause |
| `q` / `ESC` | quit |
| `r` | new game (after game over) |

## How it is built

The game is organized into small methods, each with its own `prepare`
declaration and register list – the way ibyt expects it:

- `Plot` / `Erase` – draw or clear a single point
- `PutCell` / `PeekCell` – draw or read a whole cell (scales with `Field.Cell`)
- `DrawMask` / `Blocked` – draw a 4×4 piece mask, or test it for collisions
- `TryMove` – attempt a move, then draw the piece at its old or new position
- `RowFull` / `CopyRow` / `EraseTop` / `ClearLines` – row operations
- `Spawn` / `Lock` / `Fall` / `Rotate` – piece lifecycle
- `ReadKeys` – non-blocking keyboard input
- `NewGame` / `ShowScore` – game setup and HUD

State lives in containers (`Field`, `Piece`, `Try`, `Dm`, `Line`, `Blk`,
`Game`), not in global variables. Registers are used only for the
short-lived values inside a method.

## Why it matters

The whole program is **~500 lines of ibyt** and runs **without an
operating system, without libc, without a runtime** – just the terminal,
the IBYT libraries and the CPU. It is a real, playable game written in
the same language that the compiler is written in.

For a bare-metal variant of the same game (VBE graphics, PC speaker,
own clock, **11.8 KB**), see `tetris-bare`.
