# Propane Tank Level Project

A system to monitor propane tank levels remotely using a magnetic hall effect angle sensor (AS5600). The ESPHome device measures the angle of the tank's dial gauge every few hours and transmits it via cellular (Notecard) connectivity, enabling accurate level monitoring when there is no WiFi coverage.

[View Configuration File (propane-angle.yaml)](propane-angle.yaml)

## Hardware Setup

-   **ESP32**: Running ESPHome firmware
-   **Sensor**: AS5600 magnetic hall effect angle sensor
-   **Sensor Connection**: 3D printed mount for the dial gauge
-   **Connectivity**: Blues Wireless Notecard for cellular connectivity
-   **Power**: 6v 1.8w Solar panel, ~2000 mAh LiPo battery, [CN3065](https://www.aliexpress.com/item/1005006761128554.html) solar charge controller, [TPS63802](https://www.aliexpress.com/item/32799328725.html) 3.3v lipo charge controller
-   **Housing**: Waterproof junction box or a 3D printed housing
-   **MOSFET**: [IRLZ34N](https://www.aliexpress.com/item/1005006228628494.html) mosfet for power management and a 10kΩ resistor

## Data Flow

1. ESP32 wakes up at configured intervals (default: every 2 hours)
2. Powers on the angle sensor via MOSFET to conserve battery
3. Reads angle measurement from AS5600 sensor via I2C
4. Collects additional data (temperature, battery voltage) from Notecard
5. Sends readings over cellular network using Notecard at configured intervals (default: every 6 hours)
6. Web interface displays historical data and handles calibration

## AS5600 Sensor

A high-precision magnetic hall effect angle sensor with:

-   12-bit resolution (4096 positions)
-   360° measurement capability
-   Low power consumption
-   I2C interface for data communication
-   Programmable power modes

## Wiring Instructions

**With IRLZ34N MOSFET text facing you:**

-   MOSFET left pin → ESP GPIO2
-   MOSFET left pin → 10kΩ resistor → MOSFET right pin (pull to ground)
-   MOSFET middle pin → Sensor GND
-   MOSFET right pin → ESP GND

**AS5600 Sensor:**

-   VCC → ESP 3.3V
-   GND → MOSFET middle pin
-   SDA → ESP GPIO21
-   SCL → ESP GPIO22
-   DIR → Soldered to GND (for clockwise) or to 3.3V (for counterclockwise)

**Notecard:**

-   VCC → ESP 3.3V
-   GND → ESP GND
-   TX → ESP GPIO16 (RX)
-   RY → ESP GPIO17 (TX)

## Configuration

The default configuration measures propane levels every 2 hours to conserve battery. You can adjust the measurement frequency in the ESPHome configuration file.

### Calibration Process:

1. Install the magnet on the propane tank's dial gauge
2. Align the AS5600 sensor with the magnet
3. Verify the angle readings correspond to the actual tank level
4. The system will automatically convert angle readings to propane levels

### Default Calibration

Using my above R3D 3D printed mount with an AS5600 sensor, VCC/GND facing the wire, and counterclock enabled on the DIR pin (so degrees goes up as percentages goes up) this is my default calibration data:

| Tank Level (%) | Angle (°) |
| -------------- | --------- |
| 5              | 37        |
| 10             | 63        |
| 20             | 105       |
| 30             | 134       |
| 40             | 160       |
| 50             | 183       |
| 60             | 205       |
| 70             | 229       |
| 80             | 258       |
| 90             | 296       |
| 95             | 322       |

## License

This project is open source under the MIT with attribution license.
