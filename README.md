## Quoridor game

Quoridor game implemented with Keil μVision on a LandTiger board (SoC LPC1768).

### Rules of the game
<img width="345" alt="image" src="https://github.com/user-attachments/assets/cc755e1f-2efb-4235-899a-6d7001f22c7d">

Each player is equipped with a token and 8 barriers. The game board is a 7x7 wooden square, with the peculiar feature that the lines dividing and forming the squares are grooved, allowing walls to be inserted.

Each player starts from the centre of their perimeter line (the 4th square), and the goal is to get their token to the opposite perimeter line. The player who achieves this objective first wins.

On their turn, a player has two choices:
1.	He/she can choose to move their token horizontally or vertically;
2.	He/she can choose to place a wall. The wall cannot be jumped over but must be navigated around.

At every step of the game, there are always two players: the moving player (the one that is doing the choice), and the opponent. Their roles alternate at every step.
- If two tokens (the moving player and the opponent) are facing each other, the moving player can jump over the opponent and position themselves behind them if there is no barrier behind the opponent.
  - It is not allowed to "trap" a player with the help of walls; you must always leave them a way to reach the goal.
  - A move must be chosen within 20 seconds, or else it loses the turn.

### Implementation 
The tokens must move using squares (vertically or horizontally), Figure 1. The barriers must be placed between squares, Figure 2.
<img width="480" alt="Screenshot 2024-10-07 alle 16 58 01" src="https://github.com/user-attachments/assets/0700ca07-a982-4b07-9790-c5fb5733e923">
When the system starts, to start a match, you must press INT0 (otherwise you remain in wait mode). 
From now on, you are in the match/game mode.
Each player has 20 seconds to do the move. If time expires, the player loses the turn.

At this point, remember that the player has two choices:
1. He/she moves of 1 step vertically or horizontally. To do this, use the JOYSTICK.
  1. JOY UP
  2. JOY DOWN
  3. JOY LEFT
  4. JOY RIGHT
  5. JOY SELECT: confirm the choice. The game continues with PLAYER2.
  6. The 4 squares surrounding the player (as in Figure 1) are highlighted, i.e., the player’s possible moves.
2. He/she can insert a wall to block the opposite player. To enter in this mode, the player must press KEY1. A wall will appear in the centre (the map itself is 7x7 and the wall is placed between two squares).
  1. After pressing KEY1, it appears in the centre of the 7x7 square.
  2. Use the JOYSTICK to determine the position of the wall. Once that the position is found, press JOY SELECT to confirm the position.
  3. Be careful about the wall position. A wall is sized with two squares plus a space between them.
  4. Each player has only 8 walls to position. If it ends them, a warning message will appear on the screen: “No walls available, move the token”.
  5. Pressing KEY2 rotates the wall in the current position of 90 degree.
  6. Pressing KEY1 again before confirming the position of the wall disable the wall insertion mode and return to player move mode.
