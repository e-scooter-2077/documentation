# Drop Points Planning Domain

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Area") {
    $aggregate_root("Area", area) {
        + points: Set[DropPoint]
        + shape: Shape
        + scooterNumber: Integer
        + trips: Set[Trip]
    }

    abstract $value("Shape", shape) {
        + isInArea(point: GeoPoint): Boolean
    }

    shape --o area

    $entity("Trip", trip)  {
        + travelledDistance: Distance
        + start: TripPoint
        + end: Option[TripPoint]
    }

    $value("TripPoint", point) {
        + time: Timestamp
        + position: GeoPoint
    }

    trip --o area
    trip o- point

    $value("DropPoint", dp) {
        + position: GeoPoint
        + scooterNumber: Integer
    }

    dp -o area
}

@enduml
```

## Details

### Drop Point
A drop point is a GeoPoint representing a suggested spot where to place some scooters.

## Domain Events
