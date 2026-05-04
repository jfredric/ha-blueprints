# HA Blueprints

A personal collection of Home Assistant automation blueprints.

---

## How to Import

Each blueprint has a one-click import button. Alternatively, copy any blueprint's raw URL and paste it into:

**Settings → Automations & Scenes → Blueprints → Import Blueprint**

> **Note on updates:** Home Assistant's blueprint import is a one-time snapshot. To get updates, re-import the blueprint using the same button or URL — HA will detect the newer version.

---

## Blueprints

### Automation

| Blueprint | Description | Import |
|---|---|---|
| [Door Sensor Alarm](blueprints/automation/door_sensor_alarm.yaml) | Monitors an open/close sensor and plays an alarm sound and/or announces that the named door is open. Configurable repeat counts, alert intervals, and TTS announcement. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?url=https://raw.githubusercontent.com/jfredric/ha-blueprints/main/blueprints/automation/door_sensor_alarm.yaml) |
| [Light Switch Motion Control](blueprints/automation/light_switch_motion_control.yaml) | Controls a light via motion detection with optional dusk-to-dawn light level gating. Reads physical paddle presses from Z-Wave central scene event entities for reliable manual override detection. | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?url=https://raw.githubusercontent.com/jfredric/ha-blueprints/main/blueprints/automation/light_switch_motion_control.yaml) |
---

## Requirements

- **Home Assistant** — all blueprints are developed and tested against a recent stable release. Minimum version requirements are noted per blueprint where applicable.
- **Blueprint-specific requirements** — such as integrations or add-ons — are documented in each blueprint's YAML description and in the [docs](docs/) folder.

---

## Repository Structure

```
ha-blueprints/
├── README.md
├── blueprints/
│   └── automation/
│       ├── door_sensor_alarm.yaml
│       └── light_switch_motion_control.yaml
└── docs/
    ├── door_sensor_alarm.md
    └── light_switch_motion_control.md
```
---
## Issues & Feedback
If a blueprint isn't working as expected, open an [issue](../../issues) with your HA version, the blueprint name, and a description of the problem.
