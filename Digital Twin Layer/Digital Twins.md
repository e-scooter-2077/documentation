# Digital Twins

## Scooter DT

### Model

```json
{
    "Properties": {
        "Id": Id,
        "Locked": Boolean,
        "Enabled": Boolean,
        "MaxSpeed": Number,
        "UpdateFrequency": TimeSpan,
        "StandbyThreshold": Number,
        "PowerSavingThreshold": Number,
        "PowerSavingMode": String
    },
    "Relationships": [
        {
            "displayName": "is used by",
            "description": "the device is being ridden by",
            "target": Customer
        }
    ],
    "Telemetry": {
        "Battery": Number,
        "Speed": Number,
        "Position": GeoPosition
    },
    "Components": {

    }
}
```

## Customer DT

### Model

```json
{
    "Properties": {
        "Id": Id
    },
    "Relationships": [
        {
            "displayName": "uses",
            "description": "the customer is riding",
            "target": Scooter
        }
    ],
    "Telemetry": {
    },
    "Components": {
    }
}
```
