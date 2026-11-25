# ESPHome Custom Devices Collection

A collection of my custom ESPHome devices. Each device is designed for solving a specific problem and with power efficiency in mind. Uses solar power and deep sleep modes for extended battery life.

## Projects

### Water Radar Device

A system that monitors underground water cistern levels using a mmWave radar sensor. Measures distance to water level and transmits reading to Firestore via WiFi.

[View Water Radar Documentation](water-radar-README.md) | [View Configuration](water-radar.yaml)

### Propane Angle Device

A system that monitors propane tank levels using a magnetic hall effect angle sensor (AS5600). Measures the direction of the tank's dial gauge and transmits via cellular (Notecard) connectivity.

[View Propane Angle Documentation](propane-angle-README.md) | [View Configuration](propane-angle.yaml)

### Alarm Panel (KinCony KC868-A16v3)

A bought board, not a custom build, and the exception to the solar/deep-sleep pattern: mains-powered (12 V) and on wired ethernet. Reads the house's 15 pre-wired alarm cables on 16 optoisolated dry-contact inputs and reports them to Home Assistant over the native API. Device name `alarm`, so the board serves a live zone page at http://alarm.local. Currently the tracing config (16 zones, named in the YAML as each is identified; renaming one orphans its old Home Assistant entity). Wiring, zone map and setup runbook live in the house-helper repo (`cabling/alarm-panel-setup.md`).

Flash with `esphome run alarm-panel.yaml` (USB the first time, then over the network). Needs `alarm_panel_api_key` in `secrets.yaml`; OTA uploads are encrypted with the same key, and Home Assistant asks for it when adopting the device.

Board notes, from the bench:

-   **First flash: use a USB-A port** (C-to-A adapter + A-to-C cable). A C-to-C cable from a Mac gives nothing. The USB-C is the ESP32-S3's built-in USB Serial/JTAG (`303a:1001`), with no bridge chip.
-   **Input expander addresses are swapped** from Kincony's v3 pin define on this board: `0x22` = inputs 1-8, `0x21` = inputs 9-16. To check another board, jumper input 1 to its bank GND: if Zone 09 lights instead of Zone 01, swap the two addresses.
-   **Zone changes log only at `VERBOSE`** (`'Zone 01' >> ON`) in ESPHome 2026.9, so the logger is `VERBOSE` with `i2c` held at `DEBUG` (VERBOSE i2c logs every poll).
-   The web API addresses entities by name: `curl 'http://alarm.local/binary_sensor/Zone%2001'`.

[View Configuration](alarm-panel.yaml)

### Water TOF Device (Depreciated)

A system that monitors underground water cistern levels using a Time-of-Flight sensor (VL53L1X). Struggled with humidity, so it has a pre-heating resistor setup to defog the lens. I've stopped using it and switched to mmWave radar, but left it up as an example since it works.

[View Water TOF Documentation](deprecated/water-tof-README.md) | [View Configuration](deprecated/water-tof.yaml)

## Common Components

All devices in this collection share some common design principles and components:

### Power Management

-   Solar powered (5-6v 0.5-2w panels)
-   LiPo battery (various sizes)
-   CN3065 solar charge controller (or equivalent)
-   TPS63802 3.3v buck-boost converter (or equivalent)
-   MOSFET-based power control for sensors (GND based IRLZ34N is what I use)

### ESP32 Board Options

-   ESP32 Dev Board
-   ESP32-C3 Mini
-   ESP32-C6 FireBeetle

## Shared Resources

The `common/` directory contains shared configurations that can be reused across different devices:

-   `wifi.yaml` - WiFi configuration with fallback networks
-   `mosfet_control.yaml` - MOSFET-based power control for sensors
-   Device-specific base configurations:
    -   `device_esp32dev.yaml` - Base configuration for ESP32 Dev Board
    -   `device_esp32c3_mini.yaml` - Base configuration for ESP32-C3 Mini
    -   `device_esp32c6_firebeetle.yaml` - Base configuration for ESP32-C6 FireBeetle

These shared configurations help maintain consistency across devices and reduce code duplication.

## Project Structure

Active device configurations are at the top level, as they didn't work properly in subfolders.

```
├── common/               # Shared ESPHome configurations
├── deprecated/           # Retired configs (Water TOF, Firebase water radar variants)
├── tests/                # Test configs (water radar, propane angle, propane angle C3)
├── water-radar.yaml      # Water radar device configuration
├── propane-angle.yaml    # Propane angle device configuration
├── alarm-panel.yaml      # KC868-A16v3 alarm panel (ethernet)
├── *-README.md           # Documentation for each device
├── notehub-jsonata.md    # JSONata expressions for Notehub routes
├── build.sh              # Build script that moves bin files after
└── README.md             # This file
```

## Configuration and Secrets

This project uses ESPHome's secrets management system to handle sensitive information. Create a `secrets.yaml` file in the root directory with the following variables:

```yaml
# WiFi Credentials
home_wifi_ssid: "your_home_wifi_ssid"
home_wifi_password: "your_home_wifi_password"
secondary_wifi_ssid: "your_secondary_wifi_ssid"
secondary_wifi_password: "your_secondary_wifi_password"
# Note AP mode uses the home wifi password as the default password

# Firebase Configuration (for WiFi devices)
firebase_api_key: "your_firebase_api_key"
firebase_project_id: "your_firebase_project_id"

# Notecard Configuration (for cellular devices)
notecard_project_id: "your_notecard_project_id"
notecard_org: "your_notecard_org"

# Alarm panel API encryption key (32 bytes, base64: openssl rand -base64 32)
alarm_panel_api_key: "your_base64_api_key"
```

## License

This project is licensed under the MIT License with Attribution - see the [LICENSE](LICENSE) file for details.
