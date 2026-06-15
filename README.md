# Pico Micro-Shogi (3x4 Chess)

A fully-featured, miniaturized Shogi/Chess variant for the RP2040 (Raspberry Pi Pico). Played on a 3x4 grid, this game features capture-and-drop mechanics, piece promotion, AI opponents, and an auto-save system—all controlled using just the Pico's built-in `BOOTSEL` button and a 128x64 OLED display.

<table align="center">
  <tr>
    <td align="center"><img src="webp/boot_menu_scan.webp" width="150"><br><sub><b>Boot & menu scan</b></sub></td>
    <td align="center"><img src="webp/bot-vs-bot.webp" width="150"><br><sub><b>Bot vs Bot gameplay</b></sub></td>
  </tr>
</table>

## Features

* **3 Game Modes:** Player vs Player (PvP), Player vs Bot (PvB), and Bot vs Bot (BvB).
* **Capture & Drop:** Captured enemy pieces are added to your inventory and can be dropped on empty squares as your turn.
* **Piece Promotion:** Pawns that reach the furthest rank automatically promote to Gold.
* **Auto-Save & Resume:** Game states are automatically saved to flash memory via LittleFS. If you unplug the Pico or reset it, you can resume exactly where you left off.
* **Single-Button Interface:** The game uses an auto-scanning cursor. Simply press the RP2040's native `BOOTSEL` button to make selections.
* **OLED Graphics & Animations:** Custom piece animations for moving and dropping, alongside score tracking.

## Hardware Requirements

* **Microcontroller:** Raspberry Pi Pico (or any RP2040-based board).
* **Display:** 128x64 I2C OLED Display (SSD1306).
* **Wiring:**
    * **SDA:** GPIO 28
    * **SCL:** GPIO 29
    * **VCC:** 3.3V
    * **GND:** GND
    * *No external buttons are required! Input is handled via the on-board `BOOTSEL` button.*

### Build / Wiring Diagram

<table align="center">
  <tr>
    <td align="center"><img src="blueprint.jpg" width="480"><br><sub><b>How the device is wired and assembled</b></sub></td>
  </tr>
</table>

## Software Dependencies

This project is built using the Arduino IDE with the **Earle F. Philhower RP2040 core**. Ensure you have the following libraries installed via the Arduino Library Manager:

* `Adafruit GFX Library`
* `Adafruit SSD1306`
* `Wire` (Standard I2C library)
* `LittleFS` (Included in the RP2040 core)

## How to Play

### Controls
The game uses a timed auto-scan mechanism for input.
* **Select/Confirm:** Wait for the cursor (`X` or `^`) to hover over the piece or tile you want to interact with, then **press and hold the `BOOTSEL` button** until the selection registers.
* **Turn Flow:** Select the piece you want to move (or a piece in your inventory), then select the valid destination tile.

<table align="center">
  <tr>
    <td align="center"><img src="webp/cursor_scan_board.webp" width="150"><br><sub><b>Cursor auto-scan</b></sub></td>
    <td align="center"><img src="webp/cursor_inventory.webp" width="150"><br><sub><b>Inventory cursor</b></sub></td>
    <td align="center"><img src="webp/two_step_selection.webp" width="150"><br><sub><b>Two-step selection</b></sub></td>
  </tr>
</table>

### The Pieces
The board is a 3x4 grid. Pieces are represented by the following characters:
* **K (King):** Moves 1 square in any direction. If your King is captured, you lose. If your King safely reaches the opposite end of the board, you win!
* **R (Rock/Rook):** Moves 1 square vertically or horizontally.
* **B (Bishop):** Moves 1 square diagonally.
* **P (Pawn):** Moves 1 square straight forward.
* **G (Gold):** A promoted Pawn. Moves 1 square in any direction *except* diagonally backwards.

> **Note:** On this compact 3x4 board every piece moves a single square per turn (the engine restricts all moves to a 1-cell radius). The Rook and Bishop keep their *direction* rules (orthogonal / diagonal) but not the unlimited range of standard chess/shogi.

<table align="center">
  <tr>
    <td align="center"><img src="webp/move_animation.webp" width="150"><br><sub><b>Move animation</b></sub></td>
  </tr>
</table>

### Mechanics
1.  **Capturing:** Moving onto an opponent's piece captures it. It is placed in your inventory (displayed at the bottom of the screen).
2.  **Dropping:** Instead of moving a piece on the board, you can "drop" a captured piece from your inventory onto any empty square.
3.  **Promotion:** If a Pawn (P) reaches the opponent's starting row, it automatically promotes to a Gold (G) piece. Captured promoted pieces revert back to their base state in your inventory.

<table align="center">
  <tr>
    <td align="center"><img src="webp/capture_piece.webp" width="150"><br><sub><b>Capturing a piece</b></sub></td>
    <td align="center"><img src="webp/drop_animation.webp" width="150"><br><sub><b>Dropping from inventory</b></sub></td>
    <td align="center"><img src="webp/promo_revert_capture.webp" width="150"><br><sub><b>Promoted piece reverts</b></sub></td>
    <td align="center"><img src="webp/pawn_promote_gold.webp" width="150"><br><sub><b>Pawn promotes to Gold</b></sub></td>
  </tr>
</table>

### Winning, Losing & Draws
* **Win by King's march:** get your King safely to the far end of the board.
* **Win by capture:** take the opponent's King.
* **Draw:** a player with no legal moves who is *not* in check results in a draw (`=`).

<table align="center">
  <tr>
    <td align="center"><img src="webp/king_reaches_end_win.webp" width="150"><br><sub><b>King reaches the end (win)</b></sub></td>
    <td align="center"><img src="webp/captured_loss.webp" width="150"><br><sub><b>King captured (loss)</b></sub></td>
    <td align="center"><img src="webp/draw_stalemate.webp" width="150"><br><sub><b>Draw / stalemate</b></sub></td>
    <td align="center"><img src="webp/victory_scoreboard.webp" width="150"><br><sub><b>Victory & scoreboard</b></sub></td>
  </tr>
</table>

### AI & Move Validation
In PvB and BvB modes the bot picks from the set of legal moves. When a King is under threat, the engine only offers King-saving or blocking moves.

<table align="center">
  <tr>
    <td align="center"><img src="webp/check-king-safe.webp" width="150"><br><sub><b>Only King-safe moves under check</b></sub></td>
  </tr>
</table>

## Auto-Save & Resume

The full game state is written to flash (LittleFS) every turn. Unplug or reset the Pico mid-game, power back on, and you can pick up exactly where you left off.

<table align="center">
  <tr>
    <td align="center"><img src="webp/unplug_resume.webp" width="150"><br><sub><b>Unplug and resume from saved state</b></sub></td>
  </tr>
</table>

## Installation & Flashing

1.  Install the [Earle F. Philhower RP2040 core](https://github.com/earlephilhower/arduino-pico) in your Arduino IDE.
2.  Open the `.ino` file.
3.  Ensure your board settings allocate flash memory for LittleFS (e.g., "Flash Size: 2MB (Sketch: 1MB, FS: 1MB)").
4.  Connect your Pico while holding `BOOTSEL`, select the appropriate port, and hit Upload.
