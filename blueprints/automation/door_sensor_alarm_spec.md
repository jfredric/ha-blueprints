# Door Sensor Alarm Blueprint — Specification

## Overview
A Home Assistant automation blueprint that monitors an open/close binary sensor
and plays an alarm sound and/or announces that the named door is open. Resets
automatically when the door closes.

---

## Inputs

| Input | Type | Default | Min | Max | Step | Notes |
|---|---|---|---|---|---|---|
| Door / Window Sensor | `entity` (binary_sensor) | — | — | — | — | Device classes: door, window, garage_door, opening |
| Announcement Name | `text` | `""` (blank) | — | — | — | Blank disables TTS announcement entirely |
| Audio Source | `entity` (media_player) | — | — | — | — | Where alarm and TTS play |
| Alarm Sound URL | `text` | — | — | — | — | Local path or URL to sound file (e.g. /media/local/sounds/alarm.mp3) |
| Alarm Sound Duration | `number` | `1` | `0.1` | `30` | `0.1` | Seconds. Used to time gap after playback. Supports decimals |
| Gap Between Alarm Repeats | `number` | `1` | `0.1` | `10` | `0.1` | Seconds between each alarm repeat. Supports decimals |
| Initial Alarm Play Count | `number` | `1` | `0` | `20` | `1` | Times to play alarm on first open. 0 = suppress initial alert |
| Alarm Repeats Before Announcement | `number` | `3` | `0` | `20` | `1` | Times alarm plays before each TTS announcement. 0 = skip alarm on subsequent alerts |
| Time Between Alerts | `number` (slider) | `60` | `5` | `3600` | `5` | Seconds between alert cycles |

---

## Logic

### Triggers
- `binary_sensor` state → `on` (door opened)
- `binary_sensor` state → `off` (door closed)

### Door Closed
- Call `media_player.media_stop` on the audio source
- Automation mode is `restart` so any in-progress cycle is cancelled cleanly

### Door Opened — Initial Alert
- If `initial_play_count > 0`:
  - Repeat alarm sound `initial_play_count` times
  - Between each repeat: fixed delay of `sound_duration + sound_gap` seconds

### Door Opened — Subsequent Alert Loop
- Loop `while` door sensor is `on`:
  1. Wait `alert_interval` seconds
  2. Check door is still open — bail out if closed
  3. If `repeat_before_announcement > 0`:
     - Repeat alarm sound `repeat_before_announcement` times
     - Between each repeat: fixed delay of `sound_duration + sound_gap` seconds
  4. If `announcement_name` is not blank AND door is still open:
     - Call `tts.google_translate_say` targeting the media player
     - Message format: `"The <announcement_name> is open."`

---

## Automation Settings
- `mode: restart` — if door closes and reopens mid-cycle, the sequence restarts cleanly
- `max_exceeded: silent` — suppresses warnings when restart mode drops queued runs

---

## TTS
- Hardcoded to `tts.google_translate_say`
- Targets the media player entity directly (not a separate TTS engine entity)
- `cache: false` to ensure fresh generation each time
- Announcement is skipped entirely if `announcement_name` is blank

---

## Design Decisions & Notes

- `wait_for_trigger` on media player state was removed — it proved unreliable across
  media player implementations. Replaced with a fixed delay of `sound_duration + sound_gap`.
- The `media` selector for alarm sound was evaluated but rejected — it caused double
  player selection and media browsing errors. Plain `text` input is used instead.
- `tts.speak` with a `tts.*` engine entity was evaluated but rejected — Google Translate
  TTS does not create a `tts.*` entity in HA and is only accessible via `tts.google_translate_say`.
- Piper TTS was evaluated as a local alternative but is incompatible with the target
  hardware (Intel Core 2 Duo P8600) due to NumPy x86 V2 CPU instruction requirements.
- Google Translate TTS is hardcoded for now with the intent to refactor to a configurable
  TTS service in a future version.

---

## File Structure (for GitHub / HACS)
```
ha-blueprints/
├── hacs.json
├── README.md
└── blueprints/
    └── automation/
        └── door_sensor_alarm.yaml
```

## Raw Import URL
```
https://raw.githubusercontent.com/jfredric/ha-blueprints/main/blueprints/automation/door_sensor_alarm.yaml
```
