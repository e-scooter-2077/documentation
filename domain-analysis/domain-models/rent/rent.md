# Rent Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id: EntityId
    + rentable: Boolean
    + currentlyRented: Boolean

    + rent(): Result[Nothing]
    + setFree(): Result[Nothing]
  }
}

$aggregate("Customer") {
  $aggregate_root("Customer", customer) {
    + id: EntityId
    + rents: Set[Rent]
    
    + requestRent(scooter: EntityId): Result[Rent]
    + confirmLastRent(time: Timestamp, maxDuration: Duration): Result[Rent]
    + cancelLastRent(reason: RentCancellationInfo): Result[Rent]
    + stopLastRent(reason: RentEndReason, time: Timestamp): Result[Rent]
  }

  $entity("Rent", rent) {
    + id: EntityId
    + scooter: EntityId
    + confirmation: Option[RentConfirmationInfo]
    + end: Option[RentEndInfo]
    + cancel: Option[RentCancellationInfo]
    
    + stop(timestamp: Timestamp, reason: RentEndReason): Result[Nothing]
    + confirm(timestamp: Timestamp): Result[Nothing]
  }

  $value("RentEndInfo", rentEndInfo) {
    + reason: RentEndReason
    + date: Timestamp
  }

  $value("RentCancellationInfo", rentCancellationInfo) {
    + reason: RentCancellationReason
  }

  $value("RentConfirmationInfo", rentConfirmationInfo) {
    + date: Timestamp
  }

  rentEndInfo --o rent
  rentConfirmationInfo --o rent
  rentCancellationInfo --o rent

  $enum("RentEndReason", endReason) {
    OUT_OF_AREA
    CUSTOMER
    CREDIT_EXHAUSTED
    BATTERY_LOW
  }

  $enum("RentCancellationReason", cancellationReason) {
    CREDIT_INSUFFICIENT
    SCOOTER_UNAVAILABLE
    INTERNAL_ERROR
  }

  customer "1" -- "*" rent

  rentEndInfo ..> endReason
  rentCancellationInfo ..> cancellationReason
}

rent .. scooter
@enduml
```

## Details

### Rent
**Constraints**:

- $confirmation.date + confirmation.maxEnd >= end.date \text{ if } \exists end$
- $\exists confirmation \text{ if } \exists end$

## Domain Events

- **RentRequested**:  
- **RentConfirmed**: 
- **RentStopped**: 
- **RentCanceled**: 
