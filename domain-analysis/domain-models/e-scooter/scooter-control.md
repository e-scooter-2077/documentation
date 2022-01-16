# Scooter Control & Monitor Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Scooter") {
    $aggregate_root("Scooter", scooter) {
        + id: EntityId
        + powerSavingMaxSpeed: Speed
        + desiredMaxSpeed: Speed
        + speed: Speed
        + updateFrequency: Duration
        + locked: Boolean
        + standby: Boolean
        + position: GeoPoint
        + battery: BatteryLevel
        + powerSavingThreshold: BatteryLevel

        + lock()
        + unlock()
        + setPowerSavingMaxSpeed(speed: Speed)
        + setDesiredMaxSpeed(speed: Speed)
        + setUpdateFrequency(frequency: Duration)
        + setPowerSavingThreshold(threshold: BatteryLevel)
        + getPowerMode(): PowerMode
    }

    $value("Duration") {
        + milliseconds: Integer
    }

    $value("BatteryLevel") {
        + percentage: Real
    }

    $value("Speed") {
        + metersPerSecond: Real
        + kilometersPerHour: Real
    }

    enum "PowerMode" {
        ACTIVE
        POWER_SAVING
    }

    scooter - Duration
    scooter -- PowerMode
    scooter -- BatteryLevel
    scooter -- Speed
}
@enduml
```

## Details

### Duration
**Constraints**:

- $milliseconds > 0$

### Speed
**Constraints**:

- $metersPerSecond = kilometersPerHour / 3.6$
- $metersPerSecond \geq 0$

### BatteryLevel
**Constraints**:

- $0 \leq percentage \leq 100$

!!! note
    If the scooter goes under standby, speed limits imposed by Scooter Control are ignored.

## Rules

A scooter has some properties that an employee can set to alter its behavior:

- _desiredMaxSpeed_: the maximum speed a scooter can reach at any moment;
- _powerSavingMaxSpeed_: the maximum speed a scooter can reach while in power saving mode;
- _powerSavingThreshold_: the battery level below which the scooter goes in power saving mode.

The policy of the scooters dictate that at any moment, the speed of a scooter should be below the desired max speed. Also, while in power saving mode the scooter must also obey to the powerSavingMaxSpeed rule. Therefore, at any moment the real maximum speed can be calculated as:

- _desiredMaxSpeed_, if the scooter is in _active_ mode
- _min(desiredMaxSpeed, powerSavingMaxSpeed)_, otherwise

Furthermore, a scooter can at any moment go in standby mode. This behavior is dicteted by the hardware mounted on the scooter itself, and cannot be changed easily.

## Domain Events

- **ScooterStatusChanged**: emitted after scooter status updates (namely at least one of updateFrequency, locked, maxSpeed or standby).
- **TelemetryUpdate**: emitted periodically (and frequently) with scooter telemetry updates (namely the current value of the position, battery and speed properties).
