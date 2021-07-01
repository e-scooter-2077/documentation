# Scooter State Domain Model
Lorem Ipsum Lorem

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$entity "Scooter" {
  + locked : Boolean
  + disabled : Boolean
  + maxSpeed : Speed

  + lock()
  + unlock()
  + enable()
  + disable()
  + setMaxSpeed(speed : Speed)
}
$value "Speed" {
  + metersPerSecond : Real
  + kilometersPerHour : Real

}
@enduml
```

## Details

### Speed
_metersPerSecond_ and _kilometersPerHour_ are two different units for the same speed value.
**Constraints**:

- $metersPerSecond = kilometersPerHour / 3.6$
- $metersPerSecond >= 0$

## Behaviours


### Entity_Test_Policy

## Domain Services