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
            "target": Scooter,
            "properties": {
                "rentId": Id
            }
        }
    ],
    "Telemetry": {
    },
    "Components": {
    }
}
```
