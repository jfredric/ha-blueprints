# HA Blueprints

A collection of Home Assistant automation blueprints.

---

## Blueprints

### [Light Switch Motion Control](./light_switch_motion_control/)

Controls a light via motion detection with optional dusk-to-dawn light level gating. Reads physical paddle presses directly from Z-Wave central scene event entities for reliable manual override detection.

**Key features:**
- Motion-activated with configurable auto-off timer
- Optional ambient light threshold (dusk-to-dawn)
- Manual on locks the light on; manual off restores auto mode
- Physical switch input is separate from the light output — supports remotes paired with relays
- Survives device replacements when using entity-based configuration

**Requirements:** Z-Wave JS, Zooz switch (or any Z-Wave switch with central scene event entities)

---

## Usage

Each blueprint lives in its own directory containing:

- The `.yaml` blueprint file — copy this to `config/blueprints/automation/` in your HA instance
- `README.md` — setup and configuration guide
- `SPEC.md` — full technical specification

After copying a blueprint, reload blueprints in HA via Developer Tools → YAML → Reload Blueprints, then create an automation from Settings → Automations → + Create Automation → Use a Blueprint.
