# Drop Points Planning Domain

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
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