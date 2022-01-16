# Scooter Data Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Scooter") {
    $aggregate_root("Scooter", scooter) {
        + id: EntityId
        + data: ScooterData
    }

    $value("ScooterData") {
        + serialNumber: SerialNumber
        + weight: Weight
        + model: ModelInfo
    }

    $value("SerialNumber") {
        + value: String
    }

    $value("ModelInfo") {
        + model: Name
        + vendorName: Name
    }

    $value("Name") {
        + value: String
    }

    $value("Weight") {
        + kilograms: Real
    }

    scooter o-- ScooterData
    ScooterData -- SerialNumber
    ScooterData -- ModelInfo
    ScooterData -- Weight
    ModelInfo -- Name
}
@enduml
```

## Details

### Weight
**Constraints**:

- $kilograms > 0$

### Name
**Constraints**:

- value must not be empty

### SerialNumber
**Constraints**

- value matches `^[A-Za-z0-9-]+$`

## Domain events

- **ScooterCreated**: emitted when a new scooter is registered to the system.
- **ScooterDeleted**: emitted when a scooter is removed from the system.
