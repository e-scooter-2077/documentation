# Digital Twins

## Scooter DT

### Model

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:escooter:EScooter;1",
  "@type": "Interface",
  "displayName": "E-Scooter",
  "contents": [
    {
      "@type": ["Property", "TimeSpan"],
      "name": "UpdateFrequency",
      "schema": "integer",
      "unit": "second",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "Locked",
      "schema": "boolean",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "Enabled",
      "schema": "boolean",
      "writable": true
    },
    {
      "@type": ["Property", "Velocity"],
      "name": "MaxSpeed",
      "schema": "double",
      "unit": "kilometrePerHour",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "Connected",
      "schema": "boolean"
    },
    {
      "@type": "Property", 
      "name": "Standby",
      "schema": "boolean"
    },
    {
      "@type": "Property",
      "name": "BatteryLevel",
      "schema": "double",
      "comment": "percentage semantic type is missing"
    },
    {
      "@type": ["Property","Latitude"],
      "name": "Latitude",
      "schema": "double",
      "unit": "degreeOfArc"
    },
    {
      "@type": ["Property","Longitude"],
      "name": "Longitude",
      "schema": "double",
      "unit": "degreeOfArc"
    },
    {
      "@type": ["Property","Velocity"],
      "name": "Speed",
      "schema": "double",
      "unit": "kilometrePerHour"
    }
  ]
}
```

## Customer DT

### Model

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:escooter:Customer;1",
  "@type": "Interface",
  "displayName": "Customer",
  "contents": [
    {
      "@type": "Relationship",
      "name": "is_riding",
      "displayName": "is riding",
      "target": "dtmi:com:escooter:EScooter;1",
      "properties": [
        {
          "@type": "Property",
          "name": "start",
          "schema": "dateTime"
        }
      ]

    }
  ]
}
```
