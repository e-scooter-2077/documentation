# Trip Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml

$aggregate("Trip") {
  $aggregate_root("Trip", trip) {
    + id: EntityId
    + travelledDistance: Distance
    + start: TripPoint
    + end: Option[TripPoint]
    + rent: EntityId
    + vehicle: EntityId

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

## Domain Events

- **TripStarted**: when a new trip starts.
- **TripEnded**: when a trip ends.
