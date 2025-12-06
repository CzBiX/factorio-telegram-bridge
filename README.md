# Factorio Telegram Bridge

A bridge that connects Factorio game servers with Telegram, enabling bidirectional communication between in-game chat and a Telegram group.

## Features

- 💬 **Bidirectional Chat**: Send messages between Factorio and Telegram
- 🎮 **Game Events**: Get notifications when players join or leave the server
- 🤖 **RCON Commands**: Execute Factorio commands directly from Telegram using `/` prefix
- 🔕 **Smart Notifications**: Join/leave events use silent notifications to avoid spam
- 🐳 **Docker Support**: Easy deployment with Docker

> [!WARNING]
> Enabling Telegram command execution lets anyone in the chat send RCON commands to your server. Use only in trusted groups.

## Configuration

The bridge is configured using environment variables, command-line arguments. All configuration options can be set via:
- Environment variables
- Command-line arguments (prefixed with `--`)
- `.env` file in the working directory

### Required Configuration

| Environment Variable | CLI Argument | Description | Example |
|---------------------|--------------|-------------|---------|
| `TELEGRAM_TOKEN` | `--telegram-token` | Telegram bot token from BotFather | `123456:ABC-DEF...` |
| `TELEGRAM_CHAT_ID` | `--telegram-chat-id` | Telegram chat/group ID (numeric) | `-1001234567890` |
| `RCON_PASSWORD` | `--rcon-password` | Factorio RCON password | `your-secure-password` |
| `FACTORIO_LOG_FILE` | `--factorio-log-file` | Path to Factorio server log file | `/factorio/factorio-current.log` |

If you are using `factoriotools/factorio`, check out the rcon password in the `rconpw` file.

### Optional Configuration

| Environment Variable | CLI Argument | Default | Description |
|---------------------|--------------|---------|-------------|
| `RCON_HOST` | `--rcon-host` | `127.0.0.1:27015` | Factorio RCON host and port |
| `ENABLE_TELEGRAM_COMMANDS` | `--enable-telegram-commands` | `false` | Enables executing RCON commands from Telegram |

## Installation

### Option 1: Using Docker Compose (Recommended)

Create a `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  factorio-tg-bridge:
    image: ghcr.io/czbix/factorio-telegram-bridge:latest
    container_name: factorio-tg-bridge
    environment:
      TELEGRAM_TOKEN: "your-bot-token"
      TELEGRAM_CHAT_ID: "-1001234567890"
      RCON_HOST: "factorio:27015"
      RCON_PASSWORD: "your-rcon-password"
      FACTORIO_LOG_FILE: "/factorio/factorio-current.log"
    volumes:
      - /path/to/factorio/logs:/factorio:ro
    restart: unless-stopped
    # If running alongside Factorio server in the same compose file:
    depends_on:
      - factorio

  # Optional: Your Factorio server configuration
  # factorio:
  #   image: factoriotools/factorio:latest
  #   ports:
  #     - "34197:34197/udp"
  #   volumes:
  #     - ./factorio-data:/factorio
```

Then run:

```bash
docker-compose up -d
```

### Option 2: Local Installation

#### Install and Run

```bash
cargo install https://github.com/CzBiX/factorio-telegram-bridge.git
factorio-tg-bridge --help
```

## Example Messages

### From Factorio to Telegram

- **Chat messages**: Any player message in Factorio chat will appear in Telegram
  - Format: `💬Username: message`
  
- **Player joins**: When a player joins the server (silent notification)
  - Format: `😊Username joined`
  
- **Player leaves**: When a player leaves the server (silent notification)
  - Format: `👋Username left`

### From Telegram to Factorio

- **Send messages**: Any text message sent in the Telegram chat will appear in Factorio
  - Format: `FirstName: message`
  - Messages appear in the game chat for all players to see

- **Execute commands**: Send messages starting with `/` to execute RCON commands
  - Example: `/time` - Get current game time
  - Example: `/players` - List online players
  - The bot will reply with the command output

- **Share images**: Send photos in Telegram
  - Format: `FirstName: [IMG]` appears in Factorio chat

### Command Examples

Common Factorio RCON commands you can use from Telegram:

- `/time` - Show current game time
- `/players` - List connected players
- `/seed` - Show map seed
- `/version` - Show server version
- `/evolution` - Show evolution factor

See more in [Factorio Wiki](https://wiki.factorio.com/console).
