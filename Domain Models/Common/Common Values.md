# Common Types

## EntityId
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("EntityId") {
  + id: UniqueId
}
@enduml
```

_EntityId_ represents how the identity of entities is passed in the system enabling the possibility to distinguish between one another of the same kind.

## Percentage
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Percentage") {
  + base100Value: Integer
  + base1Value: Real
}
@enduml
```

_percentage_ represents the level of a source as number in [0,1].
**Constraints**:

- $0 <= base1Value <= 1$
- $base100Value = round(base1Value * 100)$


## Speed
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Speed") {
  + metersPerSecond: Real
  + kilometersPerHour: Real
}
@enduml
```

_metersPerSecond_ and _kilometersPerHour_ are two different units for the same speed value.
**Constraints**:

- $metersPerSecond = kilometersPerHour / 3.6$
- $metersPerSecond >= 0$


## Name
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Name") {
  + value: String
}
@enduml
```

**Constraints**:

- $len(value) > 0$

## Latitude
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Latitude") {
  + value: Real
}
@enduml
```

**Constraints**:

- $value > -90 and value < 90$


## Longitude
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Longitude") {
  + value: Real
}
@enduml
```

**Constraints**:

- $value > -180 and value < 180$

## GeoPoint
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("GeoPoint") {
  + latitude: Latitude
  + longitude: Longitude
}
@enduml
```

## Distance
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Distance") {
  + kilometers: Real
}
@enduml
```

**Constraints**:

- $kilometers >= 0$


## Timestamp
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Timestamp") {
  + milliseconds: Integer
}
@enduml
```

_value_ is the number of milliseconds passed since 01/01/1970 00:00:00 (Epoch)

## Duration
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Duration") {
  + milliseconds: Integer
}
@enduml
```

_milliseconds_ is the number of milliseconds.

**Constraints**:

- $milliseconds >= 0$

## DomainError
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("DomainError"){
}
@enduml
```

## Nothing
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value("Nothing"){
}
@enduml
```

## Result
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value(Result<T>) {
  + value: T
  + error: DomainError
}
@enduml
```

