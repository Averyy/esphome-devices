# Water Cistern Project (Radar Sensor)

A system to monitor underground water cistern levels using a mmWave radar distance sensor. The ESPHome device measures the distance every few hours and transmits it via cellular (Notecard) connectivity, enabling monitoring in remote locations without WiFi coverage. The non-contact radar technology provides accurate measurements through a custom 3D printed lid. Many ultrasonic sensors struggle with underground tanks due to the temperature difference at the lid (condensation and fog forms on the sensor lens).

[View Configuration File (water-radar.yaml)](water-radar.yaml)

## Hardware Setup

-   **ESP32**: Running ESPHome firmware
-   **Sensor**: HLK-LD8001H 80GHz mmWave radar distance sensor
-   **Connectivity**: Blues Wireless Notecard for cellular connectivity
-   **Power**: 6v 1.8w Solar panel, ~2000 mAh LiPo battery, [CN3065](https://www.aliexpress.com/item/1005006761128554.html) solar charge controller, [TPS63802](https://www.aliexpress.com/item/32799328725.html) 3.3v lipo charge controller
-   **Housing**: 3D printed housing
-   **MOSFET**: [IRLZ34N](https://www.aliexpress.com/item/1005006228628494.html) mosfet which is GND based, needed because the sensor has no sleep mode and a 10kΩ resistor

## Data Flow

1. ESP32 wakes up at configured intervals (default: every 2 hours)
2. Powers on the radar sensor via MOSFET to conserve battery
3. Reads distance to water level from sensor via UART
4. Collects additional data (temperature, battery voltage) from Notecard
5. Sends readings over cellular network using Notecard
6. Web interface displays historical data and handles calibration

## HLK-LD8001H Sensor

A high-precision liquid level detection sensor using 80GHz millimeter wave radar technology with a detection range of 0.15m to 40m.

Key features:

-   Non-contact measurement
-   Works through plastic tank lids
-   Low power consumption when active
-   UART interface for data communication

Learn more: https://github.com/Averyy/esphome-custom-components

## Wiring Instructions

**With IRLZ34N MOSFET text facing you:**

-   MOSFET Gate (left pin) → ESP GPIO4
-   MOSFET Gate (left pin) → 10kΩ resistor → MOSFET Source (right pin) (pull-down resistor)
-   MOSFET Drain (middle pin) → Sensor GND
-   MOSFET Source (right pin) → ESP GND

**HLK-LD8001H Radar Sensor (antenna facing away, plug on right, top to bottom):**

Note that it doesn't follow standard wire colours (red is not power, black is not ground, etc).

-   Pin 1 (3.7V) → ESP 5V or 5V boost circuit (3.5V-5V range, ESP 5V rail only works when USB powered)
-   Pin 2 (GND) → MOSFET middle pin
-   Pin 3 → NC
-   Pin 4 → NC
-   Pin 5 (TTL_TX) → ESP GPIO13 (RX)
-   Pin 6 (TTL_RX) → ESP GPIO14 (TX)

**Notecard:**

-   VCC → ESP 3.3V
-   GND → ESP GND
-   TX → ESP GPIO16 (RX)
-   RX → ESP GPIO17 (TX)

### Calibration is required for accurate readings:

1. Measure the actual water level in your cistern at full and empty
2. Enter this value in the web interface
3. The system will calculate the offset between sensor readings and actual water levels

## Useful Commands

-	View logs:
-	esphome logs water-radar.yaml --device /dev/cu.usbserial-0001
-	Compile, flash, and then view logs:
-	esphome run water-radar.yaml --device /dev/cu.usbserial-0001

## Current

Set to let the notecard handle the sync'ing but if you want to force sync every time use:

                    // Trigger immediate sync with Notehub
                    // bool sync_success = id(notecard_component).sync_now();
                    // ESP_LOGD("main", "Sync trigger %s", sync_success ? "successful" : "failed");


## License

This project is open source under the MIT with attribution license.
