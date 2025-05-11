# Water Cistern Project (Radar Sensor)

A system to monitor underground water cistern levels using a mmWave radar distance sensor. The ESPHome device measures the distance every few hours and transmits it to a Firestore database via WiFi to view on the web. The non-contact radar technology provides accurate measurements through a custom 3D printed lid. Many ultrasonic sensors struggle with underground tanks due to the temperature difference at the lid (condensation and fog forms on the sensor lens).

[View Configuration File (water-radar.yaml)](water-radar.yaml)

## Hardware Setup

-   **ESP32**: Running ESPHome firmware
-   **Sensor**: mmWave radar distance sensor
-   **Power**: 6v 1.8w Solar panel, ~2000 mAh LiPo battery, [CN3065](https://www.aliexpress.com/item/1005006761128554.html) solar charge controller, [TPS63802](https://www.aliexpress.com/item/32799328725.html) 3.3v lipo charge controller
-   **Housing**: 3D printed housing
-   **MOSFET**: [IRLZ34N](https://www.aliexpress.com/item/1005006228628494.html) mosfet which is GND based, needed because the sensor has no sleep mode and a 10kΩ resistor

## Data Flow

1. ESP32 wakes up at configured intervals (default: every 2 hours)
2. Powers on the radar sensor via MOSFET to conserve battery
3. Reads distance to water level from sensor via UART
4. Sends readings over WiFi to Firestore database
5. Web interface displays historical data and handles calibration

## HLK LD2413 Sensor

A high-precision liquid level detection sensor using 24GHz millimeter wave radar technology with a detection range of 0.25m to 10m and accuracy of ±3mm under optimal conditions.

Key features:

-   Non-contact measurement
-   Works through plastic tank lids
-   Low power consumption when active
-   UART interface for data communication

Learn more: https://github.com/Averyy/esphome-custom-components/tree/main?tab=readme-ov-file#hlk-ld2413

## Wiring Instructions

**With IRLZ34N MOSFET text facing you:**

-   MOSFET Gate (left pin) → ESP GPIO4
-   MOSFET Gate (left pin) → 10kΩ resistor → MOSFET Source (right pin) (pull-down resistor)
-   MOSFET Drain (middle pin) → Sensor GND
-   MOSFET Source (right pin) → ESP GND

**Sensor antenna facing YOU, left to right:**

-   Sensor 3V3 → ESP 3.3V
-   Sensor GND → MOSFET middle pin
-   Sensor OT1 (UART_TX) → ESP GPIO16
-   Sensor RX (UART_RX) → ESP GPIO17
-   Sensor OT2 (IO Port) → Not used

### Calibration is required for accurate readings:

1. Measure the actual water level in your cistern at full and empty
2. Enter this value in the web interface
3. The system will calculate the offset between sensor readings and actual water levels

## License

This project is open source under the MIT with attribution license.
