# Water Cistern Project (TOF Sensor)

# Depreciated

A system to monitor underground water cistern levels using a Time-of-Flight (TOF) distance sensor (VL53L1X). The ESPHome device measures the distance every few hours and transmits it to a Firestore database via WiFi to view on the web. This implementation includes a pre-heating mechanism for the sensor lens to remove condensation and fog that typically forms in underground tanks, addressing a common issue with optical sensors.

[View Configuration File (water-tof.yaml)](water-tof.yaml)

## Hardware Setup

-   **ESP32**: Running ESPHome firmware
-   **Sensor**: TOF400C VL53L1X Time-of-Flight distance sensor
-   **Heating Element**: Multiple small resistors (10-20Ω) in parallel for lens pre-heating
-   **Power**: 6v 1.8w Solar panel, ~5000 mAh LiPo battery, [CN3065](https://www.aliexpress.com/item/1005006761128554.html) solar charge controller, [TPS63802](https://www.aliexpress.com/item/32799328725.html) 3.3v lipo charge controller
-   **Housing**: 3D printed housing
-   **MOSFETs**:
    -   [IRLZ34N](https://www.aliexpress.com/item/1005006228628494.html) for sensor and heater power control with a 10kΩ resistor

## Data Flow

1. ESP32 wakes up at configured intervals (default: every 3 hours)
2. Activates the heating element for a short period (3 minutes) to clear condensation
3. Powers on the TOF sensor via MOSFET to conserve battery
4. Reads distance to water level from sensor via I2C
5. Sends readings over WiFi to Firestore database
6. Web interface displays historical data and handles calibration

## TOF400C VL53L1X Sensor

A high-precision Time-of-Flight distance sensor with:

-   Range up to 4 meters
-   Millimeter precision under optimal conditions
-   I2C interface for data communication
-   Compact form factor
-   Infrared-based measurement technology

### Advantages and Limitations

**Advantages:**

-   Higher precision than ultrasonic sensors
-   Less affected by tank shape or obstacles
-   Compact size
-   TOF sensors are way smaller and cheaper than radar sensors

**Limitations:**

-   Requires lens heating which consumes additional power
-   Accuracy can fluctuate with temperature and humidity changes
-   Less weather proof

## Wiring Instructions

**With IRLZ34N MOSFET for sensor power (text facing you):**

-   MOSFET left pin → ESP GPIO2
-   MOSFET left pin → 10kΩ resistor → MOSFET right pin (pull to ground)
-   MOSFET middle pin → Sensor GND
-   MOSFET right pin → ESP GND

**With IRLZ34N MOSFET for heating element (text facing you):**

-   MOSFET left pin → ESP GPIO4
-   MOSFET left pin → 10kΩ resistor → MOSFET right pin (pull to ground)
-   MOSFET middle pin → Heating resistor → GND
-   MOSFET right pin → ESP GND

**TOF400C VL53L1X Sensor:**

-   VCC → ESP 3.3V
-   GND → Sensor MOSFET middle pin
-   SDA → ESP GPIO21
-   SCL → ESP GPIO22
-   XSHUT → ESP GPIO5 (optional, for sleep mode if not using a MOSFET)

**Heating Element:**

-   One end → 3.3V or 5V (depending on resistor value and desired heat)
-   Other end → MOSFET middle pin

### Calibration is required for accurate readings:

1. Measure the actual water level in your cistern at full and empty
2. Enter this value in the web interface
3. The system will calculate the offset between sensor readings and actual water levels

## Power Optimization

Due to the additional power requirements for the heating element, this implementation:

-   Uses a larger solar panel (recommended 2W) and battery to account for cloudy days
-   Minimizes heating duration to only what's necessary

## License

This project is open source under the MIT with attribution license.
