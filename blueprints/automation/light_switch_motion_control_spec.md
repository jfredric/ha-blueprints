# Spec: Light Switch Motion Control

## Overview

A Home Assistant blueprint that controls a light or switch via motion detection with optional dusk-to-dawn light level gating. Physical paddle presses are read directly from Z-Wave central scene event entities, making manual on/off detection unambiguous regardless of whether motion is active or the light is already on.

---

## Goals

- Turn a light on automatically when motion is detected, but only when ambient light is below a configured threshold (dusk-to-dawn behavior)
- Turn the light off automatically after a configurable period of no motion
- Allow a physical switch to override automation: manual on locks the light on indefinitely; manual off restores auto mode
- Support any Z-Wave switch that exposes central scene event entities (tested with Zooz)
- Be reusable across multiple switches and lights via blueprint instances

---

## Inputs

| Input | Type | Required | Default | Description |
|---|---|---|---|---|
| `light_entity` | entity (light, switch) | Yes | — | The relay/load entity to control |
| `scene_on_entity` | entity (event) | Yes | — | Central scene event entity for the on paddle |
| `scene_on_value` | select | Yes | `KeyPressed` | event_type value that triggers manual on |
| `scene_off_entity` | entity (event) | Yes | — | Central scene event entity for the off paddle |
| `scene_off_value` | select | Yes | `KeyPressed` | event_type value that triggers manual off |
| `motion_sensor` | entity (binary_sensor, motion) | Yes | — | Motion sensor entity |
| `auto_off_seconds` | number (5–3600) | Yes | `30` | Seconds of no motion before light turns off |
| `lux_sensor` | entity (sensor) | No | `""` | Ambient light level sensor |
| `lux_threshold` | number (0–100000) | Yes | `70` | Sensor value at or below which motion activates the light |
| `manual_override_helper` | entity (input_boolean) | Yes | — | Toggle helper tracking manual override state |

### Scene Value Options

Both `scene_on_value` and `scene_off_value` accept:

- `KeyPressed` — single tap (default)
- `KeyPressed2x` — double tap
- `KeyPressed3x` — triple tap
- `KeyHeldDown` — hold
- `KeyReleased` — release after hold

---

## Triggers

| ID | Platform | Entity | Condition |
|---|---|---|---|
| `manual_on` | state | `scene_on_entity` | Any state change (timestamp update) |
| `manual_off` | state | `scene_off_entity` | Any state change (timestamp update) |
| `motion_on` | state | `motion_sensor` | to: `on` |
| `motion_off` | state | `motion_sensor` | to: `off`, for: `auto_off_seconds` |

Note: Scene entity triggers fire on every press because the state is a timestamp that always changes. The `event_type` attribute is checked in the action conditions to determine the press type.

---

## Logic

### Manual On (`manual_on` trigger)
1. Confirm `event_type` attribute of `scene_on_entity` matches `scene_on_value`
2. Set `manual_override_helper` to `on`
3. Turn `light_entity` on

### Manual Off (`manual_off` trigger)
1. Confirm `event_type` attribute of `scene_off_entity` matches `scene_off_value`
2. Set `manual_override_helper` to `off`
3. Turn `light_entity` off

### Motion On (`motion_on` trigger)
1. Check `manual_override_helper` is `off`
2. Check `lux_ok` (sensor below threshold, or no sensor configured)
3. Turn `light_entity` on

### Motion Off (`motion_off` trigger)
1. Check `manual_override_helper` is `off`
2. Turn `light_entity` off

### `lux_ok` variable
- If no `lux_sensor` configured → always `true` (motion-only mode)
- If sensor configured → `true` when `states(lux_sensor) <= lux_threshold`

---

## Automation Mode

`parallel` — all four trigger branches are handled independently. No trigger blocks another.

---

## Prerequisites

### Z-Wave Scene Entities
Central scene event entities are disabled by default in HA. Before using this blueprint, enable them for each switch:

1. Settings → Devices & Services → Z-Wave JS
2. Find your device → click it
3. Locate the scene entities (named `event.{device}_scene_001`, `event.{device}_scene_002`, etc.)
4. Enable each one

### Manual Override Helper
Create one `input_boolean` helper per blueprint instance:

1. Settings → Devices & Services → Helpers → Create Helper → Toggle
2. Name it something descriptive, e.g. `motion_light_front_door_override`

---

## Known Limitations & Edge Cases

**Single combined scene entity (some older Zooz models):** Enter the same entity in both `scene_on_entity` and `scene_off_entity` and select different `scene_on_value` / `scene_off_value` to distinguish paddles. The specific values vary by model and are not yet documented — check your device's entity attributes while pressing each paddle.

**Misconfiguration — same entity and same value for on and off:** Both `manual_on` and `manual_off` branches fire simultaneously. The override flag and light state become a race condition and behavior is unpredictable. The light will likely flash and turn off. This is recoverable — just correct the configuration.

**Light level threshold unit:** The `lux_threshold` input has no unit label since it supports both percentage (0–100) and raw lux sensors. Enter the value in whatever unit your sensor reports.

---

## Version History

| Version | Notes |
|---|---|
| 1.0 | Initial release |
