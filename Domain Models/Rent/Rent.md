# Rent Domain Model

## Class Diagram
```plantuml
@startuml
!include Metamodel/Domain.Entities.metamodel.iuml
$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id : EntityId
    + rentable : Boolean
    + currentlyRented : Boolean
    + rent() : Result[Nothing]
  }
}

$aggregate("Rent") {
  $aggregate_root("Rent", rent){
    + user : EntityId
    + scooter : EntityId
    + endReason: Option[RentEndReason]
    + startDate : Timestamp
    + endDate :  Option[Timestamp]
    + end( reason : RentEndReason)
  }

  $enum("RentEndReason", end_reason){
    OUT_OF_AREA
    USER
    CREDIT_EXHAUSTED
    BATTERY_LOW
  }

  rent ..> end_reason
}
@enduml
```

## Details

### Entity_Test

### Entity_Test_Policy