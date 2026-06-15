# Pico Micro-Shogi (3x4 Chess)

A fully-featured, miniaturized Shogi/Chess variant for the RP2040 (Raspberry Pi Pico). Played on a 3x4 grid, this game features capture-and-drop mechanics, piece promotion, AI opponents, and an auto-save system—all controlled using just the Pico's built-in `BOOTSEL` button and a 128x64 OLED display.

<p align="center">
  <img src="webp/boot_menu_scan.webp" width="160" alt="Boot and single-button menu scan">
  <img src="webp/bot-vs-bot.webp" width="160" alt="Bot vs Bot gameplay">
</p>

## 📰 Features

* **3 Game Modes:** Player vs Player (PvP), Player vs Bot (PvB), and Bot vs Bot (BvB).
* **Capture & Drop:** Captured enemy pieces are added to your inventory and can be dropped on empty squares as your turn.
* **Piece Promotion:** Pawns that reach the furthest rank automatically promote to Gold.
* **Auto-Save & Resume:** Game states are automatically saved to flash memory via LittleFS. If you unplug the Pico or reset it, you can resume exactly where you left off.
* **Single-Button Interface:** The game uses an auto-scanning cursor. Simply press the RP2040's native `BOOTSEL` button to make selections.
* **OLED Graphics & Animations:** Custom piece animations for moving and dropping, alongside score tracking.

## 🛠️ Hardware Requirements

* **Microcontroller:** Raspberry Pi Pico (or any RP2040-based board).
* **Display:** 128x64 I2C OLED Display (SSD1306).
* **Wiring:**
    * **SDA:** GPIO 28
    * **SCL:** GPIO 29
    * **VCC:** 3.3V
    * **GND:** GND
    * *No external buttons are required! Input is handled via the on-board `BOOTSEL` button.*

### Build / Wiring Diagram

![Device blueprint](blueprint.jpg)

## 💻 Software Dependencies

This project is built using the Arduino IDE with the **Earle F. Philhower RP2040 core**. Ensure you have the following libraries installed via the Arduino Library Manager:

* `Adafruit GFX Library`
* `Adafruit SSD1306`
* `Wire` (Standard I2C library)
* `LittleFS` (Included in the RP2040 core)

## 🎮 How to Play

### Controls
The game uses a timed auto-scan mechanism for input.
* **Select/Confirm:** Wait for the cursor (`X` or `^`) to hover over the piece or tile you want to interact with, then **press and hold the `BOOTSEL` button** until the selection registers.
* **Turn Flow:** Select the piece you want to move (or a piece in your inventory), then select the valid destination tile.

<p align="center">
  <img src="webp/cursor_scan_board.webp" width="150" alt="Cursor auto-scan on board">
  <img src="webp/cursor_inventory.webp" width="150" alt="Inventory cursor scan">
  <img src="webp/two_step_selection.webp" width="150" alt="Two-step piece selection">
</p>

### The Pieces
The board is a 3x4 grid. Pieces are represented by the following characters:
* **K (King):** Moves 1 square in any direction. If your King is captured, you lose. If your King safely reaches the opposite end of the board, you win!
* **R (Rock/Rook):** Moves 1 square vertically or horizontally.
* **B (Bishop):** Moves 1 square diagonally.
* **P (Pawn):** Moves 1 square straight forward.
* **G (Gold):** A promoted Pawn. Moves 1 square in any direction *except* diagonally backwards.

> **Note:** On this compact 3x4 board every piece moves a single square per turn (the engine restricts all moves to a 1-cell radius). The Rook and Bishop keep their *direction* rules (orthogonal / diagonal) but not the unlimited range of standard chess/shogi.

<p align="center">
  <img src="webp/move_animation.webp" width="160" alt="Piece move animation">
</p>

### Mechanics
1.  **Capturing:** Moving onto an opponent's piece captures it. It is placed in your inventory (displayed at the bottom of the screen).
2.  **Dropping:** Instead of moving a piece on the board, you can "drop" a captured piece from your inventory onto any empty square.
3.  **Promotion:** If a Pawn (P) reaches the opponent's starting row, it automatically promotes to a Gold (G) piece. Captured promoted pieces revert back to their base state in your inventory.

<p align="center">
  <img src="webp/capture_piece.webp" width="150" alt="Capturing a piece">
  <img src="webp/drop_animation.webp" width="150" alt="Dropping a captured piece">
  <img src="webp/promo_revert_capture.webp" width="150" alt="Promoted piece reverts on capture">
  <img src="webp/pawn_promote_gold.webp" width="150" alt="Pawn promotes to Gold">
</p>

### Winning, Losing & Draws
* **Win by King's march:** get your King safely to the far end of the board.
* **Win by capture:** take the opponent's King.
* **Draw:** a player with no legal moves who is *not* in check results in a draw (`=`).

<p align="center">
  <img src="webp/king_reaches_end_win.webp" width="150" alt="King reaches the far end to win">
  <img src="webp/captured_loss.webp" width="150" alt="King captured, game lost">
  <img src="webp/draw_stalemate.webp" width="150" alt="Draw / stalemate">
  <img src="webp/victory_scoreboard.webp" width="150" alt="Victory animation and scoreboard">
</p>

### AI & Move Validation
In PvB and BvB modes the bot picks from the set of legal moves. When a King is under threat, the engine only offers King-saving or blocking moves.

<p align="center">
  <img src="webp/check-king-safe.webp" width="160" alt="Engine offers only King-safe moves under check">
</p>

## 💾 Auto-Save & Resume

The full game state is written to flash (LittleFS) every turn. Unplug or reset the Pico mid-game, power back on, and you can pick up exactly where you left off.

<p align="center">
  <img src="webp/unplug_resume.webp" width="160" alt="Unplug and resume from saved state">
</p>

## Installation & Flashing

1.  Install the [Earle F. Philhower RP2040 core](https://github.com/earlephilhower/arduino-pico) in your Arduino IDE.
2.  Open the `.ino` file.
3.  Ensure your board settings allocate flash memory for LittleFS (e.g., "Flash Size: 2MB (Sketch: 1MB, FS: 1MB)").
4.  Connect your Pico while holding `BOOTSEL`, select the appropriate port, and hit Upload.
