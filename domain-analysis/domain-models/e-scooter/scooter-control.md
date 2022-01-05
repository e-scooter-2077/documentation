# Scooter Control & Monitor Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id: EntityId
    + desiredMaxSpeed: Speed
    + status: ScooterStatus
    + telemetryStatus: ScooterTelemetryStatus

    + lock()
    + unlock()
    + setDesiredMaxSpeed(speed: Speed)
  }

  $value("ScooterStatus", status) {
    + locked: Boolean
    + maxSpeed: Speed
    + updateFrequency: Duration
    + standby: Boolean
  }

  $value("ScooterTelemetryStatus", tstatus) {
    + position: GeoPoint
    + battery: Battery
    + speed: Speed
  }

  scooter - status
  scooter -- tstatus
}
@enduml
```

## Details

### Battery
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("Battery") {
  + level: Percentage
}
@enduml
```

!!! note
    If the scooter goes under standby, speed limits imposed by Scooter Control are ignored.

## Domain Events

- **ScooterStatusChanged**: emitted after scooter status updates.
- **TelemetryUpdate**: emitted periodically with scooter telemetry updates.
