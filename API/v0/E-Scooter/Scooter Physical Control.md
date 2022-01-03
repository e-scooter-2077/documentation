# Scooter Physical Control

## Outgoing Events

### ScooterPosition  
*Fires each* every predefined certain time  
```json
{
  "id": string, //EntityId
  "position": object, //Geopoint
}
```

## Subscriptions

### Scooter Control  
- ScooterUnlocked
- ScooterLocked
- ScooterEnabled
- ScooterDisabled
- ScooterPoweModeChanged
- ScooterMaxSpeedChanged
