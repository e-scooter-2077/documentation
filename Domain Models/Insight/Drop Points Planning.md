# Drop Points Planning Domain

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$aggregate(Area) {
    $aggregate_root(Area, area) {
        + points: Set[DropPoint]
        + scooterNumber: Integer
        
    }

    $value(DropPoint, dp) {
        + position: GeoPoint
        + scooterNumber: Integer
    }

    dp -o area
}

@enduml
```

## Details

### Drop Point
