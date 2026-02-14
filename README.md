# Roon Waybar Integration

A Waybar module that displays currently playing music from Roon.

## Features

-  Shows "Artist - Track" in Waybar
-  Tooltip with Track, Artist, Album, Zone, and Volume info
-  Auto-updates every 2 seconds + event-driven updates
-  Click to open your favorite Roon TUI client

## Requirements

- Python 3
- `roonapi` Python package
- Waybar
- Roon Core on your network

## Installation

### 1. Install Python dependencies

```bash
# Arch Linux
yay -S python-roonapi

# Or with pip
pip install roonapi
```

### 2. Install scripts

```bash
# Copy scripts to ~/.local/bin
install -Dm755 roon-waybar-daemon ~/.local/bin/roon-waybar-daemon
install -Dm755 waybar-roon ~/.local/bin/waybar-roon

# Install systemd service
install -Dm644 roon-waybar.service ~/.config/systemd/user/roon-waybar.service
```

### 3. Configure Roon Core connection

Create `~/.config/roon-waybar/config.json`:

```json
{
  "roon_ip": "YOUR_ROON_CORE_IP",
  "roon_port": 9330
}
```

Replace `YOUR_ROON_CORE_IP` with your Roon Core's IP address (e.g., `192.168.1.100`).

### 4. Authorize extension

```bash
# Start the daemon
systemctl --user start roon-waybar

# Go to Roon Settings > Extensions and authorize "Waybar Roon Monitor"
```

### 5. Enable daemon on login

```bash
systemctl --user enable roon-waybar
```

## Waybar Configuration

### Add to `~/.config/waybar/config.jsonc`

```jsonc
{
  "modules-right": [
    "custom/roon",
    // ... other modules
  ],

  "custom/roon": {
    "exec": "~/.local/bin/waybar-roon",
    "return-type": "json",
    "interval": 2,
    "format": "{}",
    "tooltip": true,
    "on-click": "kitty --class roon-tui roon-tui"  // Adjust to your TUI client
  }
}
```

### Add to `~/.config/waybar/style.css`

```css
#custom-roon {
  margin-right: 15px;
  padding: 0 10px;
}
```

## Customization

### Change update interval

Edit the module config:
```jsonc
"interval": 5  // Update every 5 seconds
```

Or modify `POLL_INTERVAL_SECONDS` in `roon-waybar-daemon`.

### Change click action

Edit the `on-click` command in waybar config:
```jsonc
"on-click": "your-music-player-command"
```

### Localization

Edit text strings in `roon-waybar-daemon` to your preferred language.

## Troubleshooting

### Module shows "Roon daemon not running"

```bash
# Check daemon status
systemctl --user status roon-waybar

# View logs
journalctl --user -u roon-waybar -f
```

### Not connecting to Roon Core

1. Verify Roon Core IP in `~/.config/roon-waybar/config.json`
2. Check network connectivity: `ping YOUR_ROON_CORE_IP`
3. Ensure Roon Core port (default 9330) is accessible

### Extension not appearing in Roon

1. Restart daemon: `systemctl --user restart roon-waybar`
2. Check Roon Settings > Extensions
3. Wait up to 30 seconds for authorization timeout

## License

MIT

## Credits

Built with the [Roon API](https://github.com/roonlabs/node-roon-api).
