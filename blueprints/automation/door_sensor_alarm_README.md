# HA Blueprints

A collection of Home Assistant automation blueprints.

---

## Blueprints

### 🚪 Door Sensor Alarm

Monitors an open/close sensor and plays an alarm sound and/or announces that the named door is open. Fully configurable — works for doors, windows, garage doors, or any opening sensor.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?url=https://raw.githubusercontent.com/jfredric/ha-blueprints/main/blueprints/automation/door_sensor_alarm.yaml)

#### How it works

- **On initial open** — plays the alarm sound the configured number of times
- **On subsequent alerts** — plays the alarm sound N times, then makes a spoken announcement: *"The [name] is open."*
- **On close** — stops audio immediately and resets the cycle

#### Configuration

| Input | Description | Default |
|---|---|---|
| Door / Window Sensor | The binary sensor to monitor | — |
| Announcement Name | Name used in the spoken announcement. Leave blank to disable TTS | `""` |
| Audio Source | Media player to play alarm and announcements on | — |
| Alarm Sound URL | Path or URL to the alarm sound file (e.g. `/media/local/sounds/alarm.mp3`) | — |
| Alarm Sound Duration | Length of your sound file in seconds. Supports decimals (e.g. `0.5`) | `1` |
| Gap Between Alarm Repeats | Pause between each alarm repeat in seconds. Supports decimals | `1` |
| Initial Alarm Play Count | Times to play alarm on first open. Set to `0` to suppress | `1` |
| Alarm Repeats Before Announcement | Times alarm plays before each TTS announcement. Set to `0` to skip alarm on subsequent alerts | `3` |
| Time Between Alerts | Seconds between alert cycles | `60` |

#### Requirements

- A configured binary sensor with device class `door`, `window`, `garage_door`, or `opening`
- A media player entity
- **Google Translate TTS** integration configured in Home Assistant (for announcements)
  - Announcements can be disabled by leaving the Announcement Name blank, in which case no TTS integration is required

#### Finding your alarm sound URL

Place your sound file in `config/media/sounds/` and reference it as `/media/local/sounds/your-file.mp3`. The easiest way to confirm the exact URL is to go to **Settings → Media**, browse to your file, and copy the URL from the three-dot menu.

---

## Installation

### Option A — One-click import (recommended)

Click the **Import Blueprint** button above, or paste the raw URL directly into Home Assistant:

**Settings → Automations & Scenes → Blueprints → Import Blueprint**

```
https://raw.githubusercontent.com/jfredric/ha-blueprints/main/blueprints/automation/door_sensor_alarm.yaml
```

### Option B — Manual

Copy the YAML file to your Home Assistant config directory:

```
config/blueprints/automation/jfredric/door_sensor_alarm.yaml
```

Then reload blueprints via **Developer Tools → YAML → Reload Blueprints**.
