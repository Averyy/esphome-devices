# ESPHome Devices

Solar-powered IoT monitoring devices using ESPHome.

## Related Repository

Custom components live in `~/code/esphome-custom-components` - edit sensor behavior there, not here.

## Devices

| Device | Status | Configs |
|--------|--------|---------|
| Water Radar | Active | `water-radar.yaml`, `water-radar-c3.yaml`, `water-radar-c3-bat.yaml` |
| Propane Angle | Active | `propane-angle.yaml`, `propane-angle-c3.yaml` |
| Water TOF | **Deprecated** | `water-tof.yaml` - kept as reference, ToF sensor failed in humid environments |

Test configs (`*-test.yaml`) are for bench testing without WiFi/sleep.

## Structure

- `common/` - Shared packages (board configs, wifi, mosfet control)
- Root YAML files - Device configurations
- `secrets.yaml` - Credentials (gitignored, see secrets template in README)

## Key Patterns

- All configs use `substitutions` for `device_name` and `friendly_name`
- Board configs in `common/` define the ESP32 variant (C3/C6 configs are under construction)
- MOSFET power control requires defining `output` with `id: mosfet_pin`
- Deep sleep triggered after successful data transmission

## Data Flow

- **WiFi devices**: HTTP POST to Firestore, identified by MAC address
- **Cellular devices**: JSON via Notecard component, synced to Notehub

## Building

```bash
./build.sh water-radar.yaml
# or
esphome compile water-radar.yaml
```

## ESPHome Docs

Local copies (update each with `git pull`):

### User Docs (`~/code/esphome-docs/`)
For component configs, sensors, automations, YAML schemas.
- `content/components/` - All component documentation
- `content/components/sensor/` - Sensor platforms
- `content/guides/` - Setup and configuration guides
- `content/cookbook/` - Example configurations
- Online: https://esphome.io/

### Developer Docs (`~/code/esphome-developers-docs/docs/`)
For custom component development, Python codegen APIs, C++ base classes.
- `architecture/` - Core architecture and component structure
- `contributing/` - Development guidelines and testing
- Online: https://developers.esphome.io/

**Always reference these docs when unsure about ESPHome syntax or configuration patterns.**
