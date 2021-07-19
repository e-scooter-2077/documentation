# Scooter Control

## Outgoing Events

### ScooterUnlocked
*Fires when* a scooter is unlocked
```json
{
  "id": string //EntityId
}
```

### ScooterLocked
*Fires when* a scooter is locked
```json
{
  "id": string //EntityId
}
```

### ScooterEnabled
*Fires when* a scooter is enabled
```json
{
  "id": string //EntityId
}
```

### ScooterDisabled
*Fires when* a scooter is disabled
```json
{
  "id": string //EntityId
}
```

### ScooterPoweModeChanged
*Fires when* the power mode of a scooter is changed
```json
{
  "id": string, //EntityId
  "powerMode": string //PowerMode
}
```

### ScooterMaxSpeedChanged
*Fires when* when the max speed of a scooter is changed
```json
{
  "id": string, //EntityId
  "maxSpeed": number //Speed
}
```

## Subscriptions

### Scooter Data
- ScooterCreated
- ScooterDeleted

### Area of Service  
- ScooterWentOutsideArea
- ScooterReturnedInsideArea
- ScooterRemovedFromArea
- ScooterAssignedToArea

