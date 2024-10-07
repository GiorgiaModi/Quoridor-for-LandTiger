# Quoridor game

Quoridor game implemented with Keil μVision on a LandTiger board (SoC LPC1768). It supports multiplayer mode via CAN bus.

## Rules of the game

Each player is equipped with a token and 8 barriers. The game board is a 7x7 wooden square, with the peculiar feature that the lines dividing and forming the squares are grooved, allowing walls to be inserted.
<p align="center">
  <img width="600" alt="image" src="https://github.com/user-attachments/assets/cc755e1f-2efb-4235-899a-6d7001f22c7d">
</p>

Each player starts from the centre of their perimeter line (the 4th square), and the goal is to get their token to the opposite perimeter line. The player who achieves this objective first wins.

On their turn, a player has two choices:
1.	He/she can choose to move their token horizontally or vertically;
2.	He/she can choose to place a wall. The wall cannot be jumped over but must be navigated around.

At every step of the game, there are always two players: the moving player (the one that is doing the choice), and the opponent. Their roles alternate at every step.
- If two tokens (the moving player and the opponent) are facing each other, the moving player can jump over the opponent and position themselves behind them if there is no barrier behind the opponent.
- It is not allowed to "trap" a player with the help of walls; you must always leave them a way to reach the goal.
- A move must be chosen within 20 seconds, or else it loses the turn.
- If players are facing each other, you can jump an extra square as in the figure below.
  <p align = "center">
  <img width="300" alt="Screenshot 2024-10-07 alle 17 06 32" src="https://github.com/user-attachments/assets/28b2113b-a292-49fb-a24a-420cbac8c3c8">
  
- In case players are facing each other, and there is a wall behind the opponent, you can move to the square next to the opponent, as Figures below show.
  <p align = "center">
    <img width="403" alt="Screenshot 2024-10-07 alle 17 07 17" src="https://github.com/user-attachments/assets/d1c53333-619a-4107-8a88-1c142acffd1d">
    
## Overview
The program starts with an initial screen, and by pressing the **INT0** button, it gives access to the game menu. Here, the number of boards to use can be selected. If you wish to play in **single-player mode**, no additional setup is required. You simply need to select the **“Single Board”** mode and then choose the type of opponent (Human/NPC).

To play in **"Two Boards" mode**, the boards must be connected via the **CAN 1 controller** before pressing **INT0**; otherwise, the multiplayer option will not be available (the connection is automatically checked through a handshake between the boards). In this mode, the next menu requires you to choose the current player type, whether Human or NPC. In **multiplayer mode**, after the selection is made, the game waits for the opponent to make their choice before starting.

In summary, the available game modes are:
- **Single Board** – Human vs Human (extra point 1)
- **Single Board** – Human vs NPC
- **Two Boards** – Human vs Human
- **Two Boards** – Human vs NPC
- **Two Boards** – NPC vs NPC

## Implementation 
The tokens must move using squares (vertically or horizontally), Figure 1. The barriers must be placed between squares, Figure 2.
<p align="center">
<img width="600" alt="Screenshot 2024-10-07 alle 16 58 01" src="https://github.com/user-attachments/assets/0700ca07-a982-4b07-9790-c5fb5733e923">
</p>

When the system starts, to initiate a match, you must press the INT0 button (otherwise, the system remains in wait mode). After pressing INT0, the game menu appears, allowing you to select the game mode. Once the game mode is chosen, the match begins. The available modes will be explained later in more detail.

From this point, you are in the game mode. Each player has 20 seconds to make their move. If the time expires, the player loses their turn, and control passes to the other player.

At this point, remember that the player has two choices:

a. He/she moves of 1 step vertically or horizontally. To do this, use the JOYSTICK.
  1. JOY UP
  2. JOY DOWN
  3. JOY LEFT
  4. JOY RIGHT
  5. JOY SELECT: confirm the choice. The game continues with PLAYER2.
  6. The 4 squares surrounding the player (as in Figure 1) are highlighted, i.e., the player’s possible moves.
     
b. He/she can insert a wall to block the opposite player. To enter in this mode, the player must press KEY1. A wall will appear in the centre (the map itself is 7x7 and the wall is placed between two squares).
  1. After pressing KEY1, it appears in the centre of the 7x7 square.
  2. Use the JOYSTICK to determine the position of the wall. Once that the position is found, press JOY SELECT to confirm the position.
  3. Be careful about the wall position. A wall is sized with two squares plus a space between them.
  4. Each player has only 8 walls to position. If it ends them, a warning message will appear on the screen: “No walls available, move the token”.
  5. Pressing KEY2 rotates the wall in the current position of 90 degree.
  6. Pressing KEY1 again before confirming the position of the wall disable the wall insertion mode and return to player move mode.


## Game modes

The program starts with an initial screen, accessible by pressing the **INT0 button**, leading to the game menu. From there, players can choose between **Single Board Mode** and **Multiplayer Mode**.
<p align = "center>
<img width="469" alt="Screenshot 2024-10-07 alle 17 21 05" src="https://github.com/user-attachments/assets/6952017a-c388-4c6d-849a-dc108a285a9e">

### Single Player Mode
In **Single Board Mode**, players can select to play either against another human player or an NPC. This mode requires no additional setup. After selecting **Single Board**, the player can choose their opponent:
- **Human vs Human**
- **Human vs NPC**
<p align = "center">
<img width="453" alt="Screenshot 2024-10-07 alle 17 21 38" src="https://github.com/user-attachments/assets/1b8157ce-ad09-421f-9409-80b53893369c">

Once the choice is made, the game starts immediately.

### Multiplayer Mode
For **Two Boards Mode**, both boards must be connected via the **CAN 1 bus** before pressing **INT0**. The connection is checked automatically through a handshake between the boards. After the connection is established:
- The board where **INT0** is pressed first becomes **PLAYER 1**.
- The second board becomes **PLAYER 2**.

Both players select their roles (Human or NPC) from the menu, and the game starts after both selections are made, ensuring synchronized gameplay on both boards through a second handshake.
<p align = "center">
<img width="503" alt="Screenshot 2024-10-07 alle 17 21 56" src="https://github.com/user-attachments/assets/b6e91257-f3da-43c9-a3ac-db3f936f9ea9">


Available multiplayer combinations:
- Human vs Human
- Human vs NPC
- NPC vs NPC

### NPC
In **NPC Mode**, the computer-controlled player uses a **randomized decision-making system** to either:
- Place a wall (40% chance) to block the opponent’s progress, or
- Move the player (60% chance) using the **Breadth First Search (BFS)** algorithm to find the optimal move toward victory.

The NPC’s strategy adapts based on the game’s current state, balancing offensive and defensive plays depending on the board situation.
