# Trip Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml

$aggregate("Trip") {
    $aggregate_root("Trip", trip) {
        + distance: 
        + start: TripPoint
        + end: TripPoint
        + rider: EntityId
        + vehicle: EntityId
        
    }

    $value("TripPoint") {
        + time: Timestamp
        + position: Position
    }
}
@enduml
```

## Details

### Rent
**Constraints**:

- $startDate + maxDuration >= endDate \text{ if } \exists endDate$
