# Drop Points Planning Domain

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Area") {
    $aggregate_root("Area", area) {
        + id: EntityId
        + points: Set[DropPoint]
        + shape: Shape
        + scooterNumber: Integer
    }

    abstract $value("Shape", shape) {
        + isInArea(point: GeoPoint): Boolean
    }

    shape --o area
    $value("DropPoint", dp) {
        + position: GeoPoint
        + scooterNumber: Integer
    }

    dp -o area
}

$aggregate("Trip") {
    $aggregate_root("Trip", trip) {
        + id: EntityId
        + travelledDistance: Distance
        + start: TripPoint
        + end: Option[TripPoint]
        + areaId: EntityId
    }

        $value("TripPoint", point) {
        + time: Timestamp
        + position: GeoPoint
    }

    trip o- point
}

trip ..> area
@enduml
```

## Details

### Drop Point
A drop point is a GeoPoint representing a suggested spot where to place some scooters.

## Domain Events
