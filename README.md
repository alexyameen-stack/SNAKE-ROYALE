<div align="center">

# Snake Royale

**Two players. One arena. Battle for the crown.**

A networked multiplayer Snake game built with Python, Pygame, and TCP sockets.

**Real-time battles · Live spectating · Private chat · Custom controls**

![Snake Royale artwork](Snake_Royal/assets/snake_royale_bg.png)

[Getting Started](#getting-started) · [Gameplay](#gameplay) · [Architecture](#architecture) · [Team](#team)

</div>

## Overview

Snake Royale turns the classic Snake concept into a competitive two-player arena. Players join a shared lobby, challenge an opponent, collect pies to restore health, and compete for a crown that changes the outcome of snake collisions. Other connected users can watch the match and cheer for their favorite player.

Developed as a team project for **EECE 350 — Computing Networks** at the **American University of Beirut**, the application combines a Pygame interface with a centralized server that controls gameplay and distributes match updates to players and spectators.

## Features

| Feature | What it offers |
|---|---|
| Competitive multiplayer | Two-player matches with health, obstacles, a countdown, and a match timer. |
| Interactive lobby | Unique usernames, online player statuses, and challenge invitations that opponents can accept or reject. |
| Crown power-up | Special pies grant a temporary advantage during contact with the opposing snake. |
| Live spectators | Watch an active match, send cheers, and return to the lobby. |
| Private messaging | Exchange direct text messages with other connected users through the server. |
| Customization | Choose from ten snake colors and remap movement keys. |
| Graphical interface | Dedicated screens for connection, registration, lobby, customization, gameplay, and results, with background music. |

## Demo & Report

- [Watch the gameplay demonstration](Demonstration_Video.mp4)
- [Read the project report](Snake_Royale_Report_EECE350.pdf)

The artwork above, demonstration video, and report are included in the original project package.

## Getting Started

### Requirements

- Python 3.10 or newer is recommended.
- Pygame for the graphical client.
- A desktop environment to display the game window.
- For play across computers, devices connected to the same local network.

### 1. Prepare the project

Download or clone this repository, then open a terminal in its main folder. Keep the `Snake_Royal` directory and its `assets` folder together.

Install the client dependency:

```bash
python -m pip install pygame
```

The networking and game engine use Python's standard library. If your system uses `python3`, substitute it for `python` in the commands below.

### 2. Start the server

```bash
cd Snake_Royal
python server.py 5000
```

`5000` is an example port; another available port can be used. Leave this terminal running. The server prints connection guidance, including its local-network IP address.

### 3. Start the clients

In a separate terminal, open the `Snake_Royal` folder and run:

```bash
python client.py
```

Run a client for each player. In the connection screen, enter:

| Client location | Server IP | Port |
|---|---|---|
| On the computer running the server | `127.0.0.1` | `5000` |
| On another computer on the same network | The server computer's LAN IPv4 address | `5000` |

Use the actual port chosen when starting the server. Each client must register a different username. Select an available opponent in the lobby, send a challenge, and accept it on the other client to begin.

> Launch the client from inside `Snake_Royal` so relative asset paths resolve correctly. For a local demonstration, open two clients on one computer; use separate computers for simultaneous keyboard play.

## Gameplay

### Controls

| Action | Default key |
|---|---|
| Move up | `W` |
| Move left | `A` |
| Move down | `S` |
| Move right | `D` |

Movement keys can be reassigned in the customization screen. Immediate reversal into the opposite direction is rejected by the game engine.

### Match rules

- **Arena:** 12 columns by 14 rows.
- **Starting health:** 50 per player.
- **Match duration:** 120 seconds.
- **Pies:** Collecting a pie adds 10 health.
- **Collisions:** Damage events subtract 5 health.
- **Crown:** A power pie grants an advantage during snake-to-snake contact. The effect expires after three subsequent normal pies are collected across the match.
- **Victory:** Win when your opponent runs out of health, or finish with more health when the timer expires. Equal health at timeout produces a draw.
- **Disconnection:** If a player disconnects during a match, the remaining player is declared the winner.

The server supports **one active match at a time**, alongside connected lobby users and spectators.

## Architecture

The server owns the match state. Clients send commands and render received updates; movement, collisions, health changes, pie generation, and results are calculated centrally.

| Component | Responsibility |
|---|---|
| `server.py` | Connections, registration, lobby state, challenges, chat routing, spectators, and the match loop. |
| `protocol.py` | Message types, JSON serialization, and TCP message framing. |
| `game_engine.py` | Movement, collision handling, pie collection, crown effects, and win conditions. |
| `models.py` | Structured representations of snakes, positions, pies, obstacles, and match state. |
| `network.py` | Client socket connection, command sending, and background message reception. |
| `client.py` | Application startup, event handling, and coordination of interface screens. |
| Screen and UI modules | Menus, lobby interactions, customization, gameplay rendering, and results. |
| `constants.py` | Board dimensions, match timing, health values, and gameplay parameters. |

### Networking & concurrency

- **TCP transport:** Clients maintain socket connections to the central server.
- **Explicit framing:** Each UTF-8 JSON message is preceded by a four-byte, network-order length header. The receiver reads the complete message before decoding it.
- **Threaded server:** A thread handles each connected client, with a separate thread advancing the active match.
- **Shared-state protection:** A lock coordinates access to shared server state.
- **Background reception:** Clients receive messages on a separate thread while the interface processes events and renders.
- **State updates:** The server advances gameplay at five ticks per second and sends match state to players and spectators.

## Troubleshooting

| Issue | Check |
|---|---|
| `No module named pygame` | Install Pygame using the same Python interpreter that launches the client. |
| Client cannot connect | Confirm the server is running and the IP address and port match. For LAN play, check the host firewall permits the chosen TCP port. |
| Another computer cannot connect to `127.0.0.1` | Use the server computer's LAN IP; `127.0.0.1` refers to the current computer. |
| Missing artwork or asset errors | Run `python client.py` from inside `Snake_Royal` and keep the original `assets` folder intact. |
| Username rejected | Choose a nonempty username that another connected client is not using. |
| Challenge rejected while a match is running | Wait for the current match to finish, or join as a spectator. |

## Team

Responsibilities below follow the project's submitted report.

| Contributor | Primary contribution |
|---|---|
| **Alexandre Yammine** | Client GUI, lobby, customization, and result screens. |
| **Maher Karam** | Server core and communication protocol. |
| **Marc-Anthony Bou Mikhael** | Game engine and state rules. |

**Course:** EECE 350 — Computing Networks  
**Institution:** American University of Beirut
