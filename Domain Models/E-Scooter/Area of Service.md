# Area of Service Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$aggregate("Area of Service") {
  $aggregate_root("AreaOfService", area) {
    + id: EntityId
    + scooters: Set[EntityId]
    + shape: Shape
    + isInArea(point: GeoPoint): Boolean
  }
  abstract $value("Shape", shape) {
    + isInArea(point: GeoPoint): Boolean
  }

  shape --o area
}

$factory("ShapeFactory", shape_factory) {
  + createCircle(center: GeoPoint, radius: Distance): Shape
  + {method} ...
}
shape_factory ..> shape

$aggregate("Scooters") {
 $aggregate_root("Scooter", scooter) {
   + id: EntityId
 }
}

area .> scooter

@enduml
```

## Domain Events

- **AreaCreated**: emitted when an area is created
- **AreaDeleted**: emitted when an area is deleted
- **ScooterAssignedToArea**: emitted when a scooter is set to belong to a certain area of service.
- **ScooterRemovedFromArea**: emitted when a scooter is set to not belong to any area.
- **AreaShapeChanged**: emitted when the shape of an area of service is changed.
- **ScooterWentOutsideArea**: emitted when a scooter that belongs to an area is moved outside of its shape.
- **ScooterReturnedInsideArea**: emitted when a scooter returns inside its area of service after having moved out.
