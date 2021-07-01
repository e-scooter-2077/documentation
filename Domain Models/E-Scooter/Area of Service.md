# Area of Service Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$aggregate("Area of Service"){
  $aggregate_root("AreaOfService", area){
    + id : EntityId
    + scooters : Set<EntityId>
    + shape : Shape
    + isInArea(point : GeoPoint) : Boolean
  }
  $value("Shape", shape) {
    + isInArea(point : GeoPoint) : Boolean
  }

  shape --o area
}

$factory("ShapeFactory", shape_factory){
  + createCircle(center : GeoPoint, radius : Distance) : Shape
  + {method} ...
}
shape_factory ..> shape

$aggregate("Existing Scooters") {
 $aggregate_root("Scooter", scooter) {
   + id : EntityId
 }
}



@enduml
```

## Details

### AreaOfService
