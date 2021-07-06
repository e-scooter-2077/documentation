# Scooter Physical Control Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml

$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id: EntityId

    + lock()
    + unlock()
    + setMaxSpeed(speed: Speed)
    + setPowerSavingOn()
    + setPowerSavingOff()
  }
}
@enduml
```

## Details

### Scooter
Exposes the possible action to do on the physical Scooter.
All the actions are idempotent.