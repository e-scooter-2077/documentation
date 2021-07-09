# Trip Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml

$aggregate("Trip") {
    $aggregate_root("Trip", trip) {
        + travelledDistance: Distance
        + start: TripPoint
        + end: Option[TripPoint]
        + rent: EntityId
        + vehicle: EntityId
s
        + end(point: TripPoint): Result[Nothing]
        + addDistance(distance: Distance): Result[Nothing]
    }

    $value("TripPoint", point) {
        + time: Timestamp
        + position: GeoPoint
    }

    point -o trip
}
@enduml
```
