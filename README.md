# oscl gooey

**A lightweight, fully featured GUI for sending, monitoring, and receiving [Open Sound Control (OSC)](https://opensoundcontrol.stanford.edu) messages.** Built with Python, designed for speed and simplicity, and optimized for [TheaterGWD / annieData](https://github.com/halfsohappy/TheaterGWD) sensor devices.

Runs in your browser. One command to start. No complicated setup.

---

## Features

- **Send** — Compose and send OSC messages with typed arguments (int, float, string). Send once or on a repeating interval. Send batch messages from JSON.
- **Receive** — Listen on any UDP port for incoming OSC messages. Filter by address pattern (include or exclude). Real-time display with auto-scroll.
- **Bridge** — Forward OSC messages between ports/hosts with optional filtering. Great for routing sensor data to multiple destinations.
- **TheaterGWD Presets** — One-click commands for annieData devices: blackout, restore, list patches, create messages, start/stop patches, save/load config, and more.
- **Monitor** — Full message log with live message rate, filtering, and color-coded direction tags (send/recv/bridge).
- **Real-time** — WebSocket-powered live updates. Messages appear instantly as they arrive.
- **Responsive** — Works on desktop, tablet, and mobile browsers.

---

## Quick Start

### Prerequisites

- **Python 3.8+** — [Download Python](https://www.python.org/downloads/)
  - macOS: `brew install python` or pre-installed on most versions
  - Ubuntu/Debian: `sudo apt install python3 python3-pip python3-venv`
  - Windows: Download from [python.org](https://www.python.org/downloads/) (check "Add to PATH" during install)

That's it. Python is the only requirement.

### Install & Run (3 steps)

**Step 1: Clone the repository**

```bash
git clone https://github.com/halfsohappy/oscl_gooey.git
cd oscl_gooey
```

**Step 2: Install dependencies**

```bash
pip install -r requirements.txt
```

Or, if you prefer a virtual environment (recommended):

```bash
python3 -m venv venv
source venv/bin/activate        # macOS/Linux
# venv\Scripts\activate         # Windows
pip install -r requirements.txt
```

**Step 3: Run**

```bash
python run.py
```

The GUI opens automatically in your browser at **http://127.0.0.1:5000**.

---

## Install Guide (Detailed)

### macOS

```bash
# Install Python if not already installed
brew install python

# Clone and set up
git clone https://github.com/halfsohappy/oscl_gooey.git
cd oscl_gooey
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Run
python run.py
```

### Ubuntu / Debian / Raspberry Pi

```bash
# Install Python and pip
sudo apt update
sudo apt install python3 python3-pip python3-venv

# Clone and set up
git clone https://github.com/halfsohappy/oscl_gooey.git
cd oscl_gooey
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Run
python run.py
```

### Windows

1. Download and install Python from [python.org](https://www.python.org/downloads/)
   - **Check "Add Python to PATH"** during installation
2. Open Command Prompt or PowerShell:

```powershell
git clone https://github.com/halfsohappy/oscl_gooey.git
cd oscl_gooey
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python run.py
```

### Command-Line Options

```
python run.py [OPTIONS]

Options:
  --port PORT       Web server port (default: 5000)
  --host HOST       Web server host (default: 127.0.0.1)
  --no-browser      Don't auto-open browser on startup
  --debug           Enable debug mode
```

**Examples:**

```bash
# Use a different port
python run.py --port 8080

# Allow access from other devices on your network
python run.py --host 0.0.0.0

# Headless mode (no browser popup)
python run.py --no-browser
```

---

## Usage Guide

### Send Tab

Send individual OSC messages or batch messages from JSON.

| Field | Description |
|-------|-------------|
| **Host** | Target IP address (e.g., `127.0.0.1`, `192.168.1.50`) |
| **Port** | Target UDP port (1–65535) |
| **OSC Address** | Message address path, must start with `/` (e.g., `/sensor/x`) |
| **Arguments** | Space-separated values — integers (`42`), floats (`3.14`), strings (`hello`) |
| **Interval** | Repeat interval in milliseconds for continuous sending |

**Buttons:**
- **Send Once** — Send a single message
- **Start Repeat** — Send the message continuously at the specified interval
- **Stop** — Stop repeated sending

**JSON Batch Send** — Paste a JSON array of messages:
```json
[
  { "address": "/light/dimmer", "args": [255] },
  { "address": "/light/color", "args": [1.0, 0.5, 0.0] },
  { "address": "/status", "args": ["ready"] }
]
```

### Receive Tab

Listen for incoming OSC messages on a specified port.

| Field | Description |
|-------|-------------|
| **Listen Port** | UDP port to listen on (default: 9000) |
| **Filter** | Include filter: `sensor` shows only addresses containing "sensor". Exclude filter: `-debug` hides addresses containing "debug" |

You can run multiple listeners on different ports simultaneously.

### Bridge Tab

Forward OSC messages between endpoints with optional filtering.

| Field | Description |
|-------|-------------|
| **Listen Port** | Port to receive messages on |
| **Output Host** | Destination IP to forward messages to |
| **Output Port** | Destination port |
| **Filter** | Same include/exclude filtering as Receive |

### TheaterGWD Tab

Pre-configured controls for [annieData](https://github.com/halfsohappy/TheaterGWD) sensor devices.

**Device Settings:**
- **Device Host** — IP address of your annieData device
- **Device Port** — OSC port (default: 8000)
- **Device Name** — Device identifier used in OSC addresses (default: `bart`)

**Quick Commands:**
| Button | OSC Address | Description |
|--------|-------------|-------------|
| ⬛ Blackout | `/annieData/{device}/blackout` | Stop all sensor output |
| 🔆 Restore | `/annieData/{device}/restore` | Resume sensor output |
| 💾 Save | `/annieData/{device}/save` | Save config to NVS |
| 📂 Load | `/annieData/{device}/load` | Load config from NVS |
| 📊 Status | `/annieData/{device}/status/config` | Request device status |
| 🗑️ NVS Clear | `/annieData/{device}/nvs/clear` | Clear NVS storage |
| 📋 Messages | `/annieData/{device}/list/msgs` | List configured messages |
| 📋 Patches | `/annieData/{device}/list/patches` | List configured patches |
| 📋 All | `/annieData/{device}/list/all` | List everything |

**Patch Control** — Start/stop named patches (groups of sensor messages).

**Create Message** — Configure a sensor stream:
- Choose a sensor value (accelX, accelY, accelZ, gyroX, gyroY, gyroZ, baro, eulerX, eulerY, eulerZ, etc.)
- Set target IP, port, and OSC address for where the sensor data should be sent

**Device Reply Listener** — Listen for `/reply/{device}/...` responses from the device.

### Monitor Tab

Full message log for all directions (send, receive, bridge).

- **Auto-scroll** — Keeps the log scrolled to the latest message
- **Filter** — Real-time text filter on message addresses
- **Stats** — Live message count and messages-per-second rate
- **Clear** — Reset the log

---

## Architecture

```
oscl_gooey/
├── run.py                  # Entry point — starts the web server
├── requirements.txt        # Python dependencies
├── app/
│   ├── __init__.py
│   ├── main.py             # Flask routes, API endpoints, SocketIO events
│   ├── osc_handler.py      # OSC engine: send, receive, bridge, monitor
│   ├── templates/
│   │   └── index.html      # Single-page GUI
│   └── static/
│       ├── css/
│       │   └── style.css   # Stylesheet (Annabel Portfolio design language)
│       └── js/
│           └── app.js      # Frontend logic, WebSocket, UI interactions
├── src/                    # Original Common Lisp CLI source (preserved)
├── t/                      # Original CL tests (preserved)
├── oscl.asd                # Original CL system definition (preserved)
└── oscl.ros                # Original CL Roswell script (preserved)
```

**Tech Stack:**
- **Backend:** Python 3 + Flask + Flask-SocketIO
- **OSC Library:** python-osc (reliable, well-maintained)
- **Real-time:** WebSocket via Socket.IO
- **Frontend:** Vanilla HTML/CSS/JS (no build step, no npm)

---

## Troubleshooting

### Port already in use

```
OSError: [Errno 98] Address already in use
```

Another process is using the port. Either stop that process or use a different port:
```bash
python run.py --port 8080          # Different web server port
```

For OSC listener port conflicts, stop the existing listener from the GUI before starting a new one on the same port.

### Can't receive messages from another device

Make sure:
1. Your firewall allows incoming UDP on the listen port
2. The sending device is targeting your machine's IP (not `127.0.0.1` unless local)
3. Run with `--host 0.0.0.0` if accessing the GUI from another device on your network

### Messages not showing up

- Check that the listener is active (green dot in the Active Listeners list)
- Check your filter — an active filter may be hiding messages
- Make sure the sending device is targeting the correct IP and port

---

## Original CLI Tool

The original `oscl` command-line tool (Common Lisp) is preserved in the `src/` and `t/` directories. See the [original oscl documentation](https://github.com/ogrew/oscl) for CLI usage. The GUI does not depend on the CLI — it uses Python's `python-osc` library directly.

---

## License

[MIT License](https://github.com/ogrew/oscl/blob/main/LICENSE)
