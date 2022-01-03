# Scooter Data Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id: EntityId
    + serialNumber: SerialNumber
    + weight: Weight
    + model: ModelInfo
  }
}
@enduml
```

## Details

### Scooter

### SerialNumber
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("SerialNumber") {
  + value: String
}
@enduml
```
**Constraints**:

- `^[A-Za-z0-9-]+$`

### Weight
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Weight") {
  + kilograms: Real
}
@enduml
```
**Constraints**:

- $kilograms > 0$


### ModelInfo
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("ModelInfo") {
  + model: Name
  + vendorName: Name
}
@enduml
```

## Domain events

- **ScooterCreated**: emitted when a new scooter is registered to the system.
- **ScooterDeleted**: emitted when a scooter is removed from the system.
