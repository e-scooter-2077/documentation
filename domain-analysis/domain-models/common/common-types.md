# Common Types

This section contains a set of value objects used throughout the domain models of the identified bounded contexts, to avoid repeating them in each diagram.

## Nothing
Represents the absence of any value, _nothingness_.
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("Nothing")
@enduml
```

## Result
Represents the outcome of a domain operation, which may result in a success (in which case the result contains a _value_ of type T) or in a failure (which results in a _DomainError_).

```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
abstract $value("Result")<T>
$value("Success")<T> extends Result {
    + value: T
}
$value("Failure")<T> extends Result {
    + error: DomainError
}

abstract $value("DomainError")

$value("ConcreteErrorA") extends DomainError
$value("ConcreteErrorB") extends DomainError

Failure "1" o-- "0..1" DomainError
@enduml
```

## EntityId
Represents how the identity of entities is passed in the system enabling the possibility to distinguish between one another of the same kind.

```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("EntityId") {
  + id: UniqueId
}
@enduml
```

## GeoPoint
Defines a geographical coordinate as a 2D value (using latitude and longitude).
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("Latitude") {
    + value: Real
}

$value("Longitude") {
    + value: Real
}

$value("GeoPoint") {
    + latitude: Latitude
    + longitude: Longitude
}

GeoPoint o-- Latitude
GeoPoint o-- Longitude
@enduml
```

**Constraints**:

- $-90 < latitude < 90$
- $-180 < longitude < 180$

## Distance
Represents a linear, non-directed (meaning an absolute value) distance.

```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("Distance") {
    + kilometers: Real
}
@enduml
```

**Constraints**:

- $kilometers \geq 0$

## Timestamp
Represents a unique point in time, independent of any time zone.

```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$value("Timestamp") {
    + milliseconds: Integer
}
@enduml
```

_value_ is the number of milliseconds passed since 01/01/1970 00:00:00 (Epoch).
