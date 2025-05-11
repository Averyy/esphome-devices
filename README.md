# ESPHome Custom Devices Collection

A collection of my custom ESPHome devices for various home monitoring and automation tasks. Each device is designed with power efficiency in mind, typically using solar power and deep sleep modes for extended battery life.

## Projects

### Water Radar Device

A system that monitors underground water cistern levels using a mmWave radar sensor. Transmits readings to Firestore via WiFi. Uses non-contact radar technology, solving condensation issues that affect ultrasonic sensors.

[View Water Radar Documentation](water-radar/README.md) | [View Configuration](water-radar/water-radar.yaml)

### Propane Angle Device

A system that monitors propane tank levels using a magnetic hall effect angle sensor (AS5600). Measures the tank's dial gauge angle and transmits data via cellular (Notecard) connectivity, ideal for locations without WiFi coverage.

[View Propane Angle Documentation](propane-angle/README.md) | [View Configuration](propane-angle/propane-angle.yaml)

### Water TOF Device

A system that monitors underground water cistern levels using a Time-of-Flight sensor (VL53L1X). Includes a pre-heating mechanism to remove condensation from the sensor lens. Transmits readings to Firestore via WiFi. Struggles in humid environments (my main use case is an underground cistern), so I've discontinued development of it.

[View Water TOF Documentation](water-tof/README.md) | [View Configuration](water-tof/water-tof.yaml)

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

Will organize to have individual folders per project soon.

```
├── common/              # Shared ESPHome configurations
├── propane-angle/       # Propane tank level monitoring using a magnetic angle sensor
├── water-radar/         # Water level monitoring device using a Radar sensor
├── water-tof/           # Water level monitoring device using a Time-of-Flight sensor
├── build.sh           	# Build script that moves bin files after
└── README.md           # This file
```

## Configuration and Secrets

This project uses ESPHome's secrets management system to handle sensitive information. Create a `secrets.yaml` file in the root directory with the following variables:

```yaml
# WiFi Credentials
home_wifi_ssid: "your_home_wifi_ssid"
home_wifi_password: "your_home_wifi_password"
secondary_wifi_ssid: "your_secondary_wifi_ssid"
secondary_wifi_password: "your_secondary_wifi_password"

# Firebase Configuration (for Water Radar and Water TOF devices)
firebase_api_key: "your_firebase_api_key"
firebase_project_id: "your_firebase_project_id"

# Notecard Configuration (for Propane Angle device)
notecard_project_id: "your_notecard_project_id"
notecard_org: "your_notecard_org"
```

## License

This project is open source under the MIT with attribution license.
