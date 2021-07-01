# Common Types

## EntityId
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value "EntityId" {
  + id : UniqueId
}
@enduml
```
_EntityId_ represents how the identity of entities is passed in the system enabling the possibility to distinguish between one another of the same kind.

## Percentage
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$value "Percentage" {
  + base100Value : Integer
  + base1Value : Real
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
$value "Speed" {
  + metersPerSecond : Real
  + kilometersPerHour : Real
}
@enduml
```
_metersPerSecond_ and _kilometersPerHour_ are two different units for the same speed value.
**Constraints**:

- $metersPerSecond = kilometersPerHour / 3.6$
- $metersPerSecond >= 0$

