# Trip Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml

$aggregate("Trip") {
    $aggregate_root("Trip", trip) {
        + distance: Distance
        + start: TripPoint
        + end: TripPoint
        + rent: EntityId
        + vehicle: EntityId
    }

    $value("TripPoint", point) {
        + time: Timestamp
        + position: Position
    }

    point -o trip
}
@enduml
```

## Details

### Rent
**Constraints**:

- $startDate + maxDuration >= endDate \text{ if } \exists endDate$
