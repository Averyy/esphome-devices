# Notehub JSONata Transforms

Reference for JSONata expressions used in Notehub routes.

## Dynamic Transform (Recommended)

Handles both water and propane device types dynamically:

```jsonata
$merge([
{
    "deviceId": $substring(device, $length(device)-6),
    "type": $exists(body.type) ? body.type : "propane",
    "temperature": body.temperature,
    "batteryVoltage": body.batteryVoltage,
    "timestamp": $exists(when) ? when : received,
    "fleetKey": "fk_your_fleet_key_here",
    "location": {
        "latitude": best_lat,
        "longitude": best_lon
    }
},
$exists(body.angle) ? {"angle": body.angle} : {},
$exists(body.distance) ? {"distance": body.distance} : {}
])
```

## Static Propane Transform (Legacy)

Hardcoded for propane devices only:

```jsonata
{
    "deviceId": $substring(device, $length(device)-6),
    "type": "propane",
    "angle": body.angle,
    "temperature": body.temperature,
    "batteryVoltage": body.batteryVoltage,
    "timestamp": ($exists(when) ? when : received),
    "fleetKey": "fk_your_fleet_key_here",
    "location": {
        "latitude": best_lat,
        "longitude": best_lon
    }
}
```

## Example Input

```json
{
    "event": "a9cf6f3b-fcb4-89dd-b2e1-5cc23ad7f758",
    "when": 1769953247,
    "file": "sensors.qo",
    "body": {
        "type": "water",
        "distance": 161.525391,
        "batteryVoltage": 3.785764,
        "temperature": -11
    },
    "device": "dev:860000000000000",
    "best_lat": 40.7128,
    "best_lon": -74.0060
}
```

## Example Output

```json
{
    "deviceId": "000000",
    "type": "water",
    "distance": 161.525391,
    "temperature": -11,
    "batteryVoltage": 3.785764,
    "timestamp": 1769953247,
    "fleetKey": "fk_your_fleet_key_here",
    "location": {
        "latitude": 40.7128,
        "longitude": -74.0060
    }
}
```
