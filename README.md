# NeuroShield Eye 👁️

> **Intelligent Eye Protection & Screen Health Management for Windows 11**

[![Python](https://img.shields.io/badge/Python-3.12%2B-blue?logo=python)](https://python.org)
[![PyQt6](https://img.shields.io/badge/GUI-PyQt6-green)](https://pypi.org/project/PyQt6/)
[![Platform](https://img.shields.io/badge/Platform-Windows%2011-lightblue?logo=windows)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)
[![SQLite](https://img.shields.io/badge/Database-SQLite-orange)](https://sqlite.org)

NeuroShield Eye is a modular, privacy-first desktop application for Windows 11 that protects your vision and improves screen habits. It runs silently in your system tray, monitoring your screen time, filtering blue light, reminding you to take breaks, and logging your eye health data — all locally, with zero telemetry.

---

## ✨ Features

### 🔵 Blue Light Filter Engine
- Transparent fullscreen overlay across all monitors
- Adjustable color temperature: 2000K (warm amber) → 6500K (neutral)
- Adjustable opacity: 0–80%
- Click-through overlay — zero interaction interference
- Flicker-free rendering with QPainter compositing

### ⏱️ Smart Break Timer
- **20-20-20 Rule**: Every 20 minutes, look 20 feet away for 20 seconds
- **Custom Mode**: Define your own work/break intervals
- Fullscreen break overlay with animated countdown
- Optional forced break (cannot be dismissed early)
- Sound notification on break start
- Thread-safe non-blocking timer engine

### 🔆 Software Dim Engine
- Dim your screen below the hardware brightness minimum
- Per-monitor overlay dimming
- Smooth opacity transitions

### 🎯 Focus Mode
- Automatically dims inactive windows
- Highlights the active window
- Optional global grayscale rendering
- Minimizes distraction without closing apps

### 📊 Daily Analytics Dashboard
- Real-time screen time tracking
- Break completion rate and streak counter
- Missed break logging
- Weekly summary chart (PyQtGraph)
- All data stored in local SQLite database

### 🪑 Posture Reminder
- Configurable interval popups
- Non-blocking, auto-dismissing notifications
- Custom reminder messages

### ⚙️ Settings Panel
- Dark-themed tabbed settings UI
- Apply changes without restart
- Export/reset to defaults
- JSON-backed persistent config

### 🖥️ System Tray
- Runs fully in background
- Right-click context menu for all features
- Optional Windows startup registration (via registry)

---

## 🏗️ Architecture

NeuroShield Eye is built on a **modular OOP architecture** where each feature is an independent, self-contained Python module communicating via Qt signals. The main process orchestrates all modules through a central controller.

```
┌──────────────────────────────────────────────────────────────────┐
│                      NeuroShield Eye                             │
│                                                                  │
│  ┌─────────────┐    ┌──────────────┐    ┌──────────────────┐    │
│  │  TrayManager│───▶│  AppController│◀───│  SettingsManager │    │
│  └─────────────┘    └──────┬───────┘    └──────────────────┘    │
│                            │                                     │
│         ┌──────────────────┼───────────────────┐                 │
│         │                  │                   │                 │
│  ┌──────▼──────┐  ┌────────▼──────┐  ┌────────▼────────┐        │
│  │BlueLightFilter│  │  BreakTimer  │  │   DimEngine     │        │
│  └─────────────┘  └───────────────┘  └─────────────────┘        │
│                                                                  │
│  ┌─────────────┐  ┌───────────────┐  ┌─────────────────┐        │
│  │  FocusMode  │  │PostureReminder│  │ DatabaseManager │        │
│  └─────────────┘  └───────────────┘  └─────────────────┘        │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │             DashboardWindow (PyQt6 UI)                   │    │
│  └──────────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────────┘
```

### Module Responsibilities

| Module | Responsibility |
|--------|---------------|
| `main.py` | Entry point, AppController, lifecycle management |
| `tray_manager.py` | System tray icon, context menu, signals |
| `blue_light_overlay.py` | Fullscreen color-temperature overlay |
| `break_timer.py` | 20-20-20 / custom break timer engine |
| `dim_engine.py` | Software brightness dimming overlays |
| `focus_mode.py` | Window focus dimming / grayscale |
| `posture_reminder.py` | Timed posture notification popups |
| `dashboard_window.py` | Analytics UI, charts, stats |
| `database_manager.py` | SQLite CRUD for all telemetry |
| `settings_manager.py` | Config load/save/validate |
| `logger.py` | Rotating file + console logger |

---

## 📋 Requirements

- **Windows 11** (64-bit)
- **Python 3.12+**
- **pip** package manager

---

## 🚀 Installation

```bash
# 1. Clone the repository
git clone https://github.com/avinabmaharjan/NeuroShield-Eye.git
cd NeuroShield-Eye

# 2. Create a virtual environment (recommended)
python -m venv venv
venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the application
python src/main.py
```

---

## 🖱️ Usage

1. On launch, NeuroShield Eye minimizes to the **system tray** (bottom-right taskbar area).
2. **Right-click** the tray icon to access all features.
3. Click **Open Dashboard** to view your screen health analytics.
4. Click **Settings** to configure intervals, colors, and behavior.
5. The app auto-starts with Windows if enabled in Settings.

---

## 📦 Build Executable (PyInstaller)

```bash
# Install PyInstaller
pip install pyinstaller

# Build single-file executable
pyinstaller --noconfirm --onefile --windowed \
  --icon=assets/tray_icon.ico \
  --add-data "assets;assets" \
  --add-data "config;config" \
  --name "NeuroShieldEye" \
  src/main.py

# Output: dist/NeuroShieldEye.exe
```

> **Note**: Add `--uac-admin` if registry write for startup is needed.

---

## 🗂️ Project Structure

```
NeuroShield-Eye/
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
├── config/
│   └── default_config.json       # Default settings (shipped with app)
├── assets/
│   ├── tray_icon.ico             # System tray icon
│   └── sounds/
│       └── break_alert.wav       # Break notification sound
├── src/
│   ├── main.py                   # Entry point + AppController
│   ├── tray/
│   │   └── tray_manager.py       # System tray logic
│   ├── overlay/
│   │   └── blue_light_overlay.py # Blue light filter overlay
│   ├── break_system/
│   │   └── break_timer.py        # Break timer engine
│   ├── brightness/
│   │   └── dim_engine.py         # Software dim overlay
│   ├── focus/
│   │   └── focus_mode.py         # Focus mode manager
│   ├── posture/
│   │   └── posture_reminder.py   # Posture reminder
│   ├── dashboard/
│   │   └── dashboard_window.py   # Analytics dashboard
│   ├── database/
│   │   └── database_manager.py   # SQLite manager
│   ├── settings/
│   │   └── settings_manager.py   # Config manager
│   └── utils/
│       └── logger.py             # Logging setup
└── build/                        # PyInstaller output
```

---

## 🔒 Privacy

NeuroShield Eye is **100% offline**. No network requests, no telemetry, no accounts. All data lives in a local SQLite file at `%APPDATA%\NeuroShieldEye\data.db`.

---

## 🔮 Future Improvements

- [ ] AI-powered blink rate detection via webcam
- [ ] Ambient light sensor integration (for auto-brightness)
- [ ] Custom break screen themes
- [ ] Export analytics to CSV/PDF
- [ ] Multi-language support
- [ ] Notification Center integration (Windows 11 Action Center)
- [ ] Profile switching (work / gaming / night)
- [ ] macOS/Linux port

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.

1. Fork the repo
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

---

*Built with ❤️ for developers who stare at screens too long.*
