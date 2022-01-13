# Rent Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml
$aggregate("Scooter") {
  $aggregate_root("Scooter", scooter) {
    + id: EntityId
    + ongoingRentId: Option[EntityId]
    + isInStandby: Boolean
    + isOutOfService: Boolean
    + isEnabled: Boolean

    + rent(rentId: EntityId): Result[Nothing]
    + clearRent()
    + enable()
    + disable()
    + enterStandby()
    + leaveStandby()
    + enterAreaOfService()
    + leaveAreaOfService()
  }
}

$aggregate("Rent") {
  $aggregate_root("Rent", rent) {
    + id: EntityId
    + scooterId: EntityId
    + customerId: EntityId
    + requestTimestamp: Timestamp
    + confirmationInfo: Option[RentConfirmationInfo]
    + cancellationInfo: Option[RentCancellationInfo]
    + stopInfo: Option[RentStopInfo]

    + confirm(confirmationInfo: ConfirationInfo): Result[Nothing]
    + cancel(cancellationInfo: CancellationInfo): Result[Nothing]
    + stop(stopInfo: StopInfo): Result[Nothing]
  }

  $value("RentConfirmationInfo", confirmationInfo) {
    + date: Timestamp
  }

  $value("RentCancellationInfo", cancellationInfo) {
    + reason: RentCancellationReason
  }

  $value("RentStopInfo", stopInfo) {
    + reason: RentStopReason
    + date: Timestamp
  }

  $enum("RentCancellationReason", cancellationReason) {
    CREDIT_INSUFFICIENT
    SCOOTER_UNAVAILABLE
    INTERNAL_ERROR
  }

  $enum("RentStopReason", stopReason) {
    STOPPED_BY_CUSTOMER
    OUT_OF_AREA
    CREDIT_EXHAUSTED
    STANDBY
    DISABLED
    NOT_RENTABLE
  }

  rent "1" -- "0..1" confirmationInfo
  rent "1" -- "0..1" cancellationInfo
  rent "1" -- "0..1" stopInfo
  stopInfo "*" -- "1" stopReason
  cancellationInfo "*" -- "1" cancellationReason
}

$aggregate("Customer") {
  $aggregate_root("Customer", customer) {
    + id: EntityId
    + ongoingRentId: Option[EntityId]

    + startRent(rentId: EntityId): Result[Nothing]
    + endRent(): Result[Nothing]
  }
}

rent .. scooter
rent .. customer
@enduml
```

## Domain Events

### Rent aggregate
- **RentRequested**: When a customer requests a rent.
- **RentConfirmed**: When a rent is confirmed.
- **RentStopped**: When a rent is stopped.
- **RentCancelled**: When a rent is cancelled.
- **RentEnded**: When a rent is ended either by cancellation or by stop.

### Scooter aggregate
- **ScooterEnabled**: When a scooter is enabled.
- **ScooterDisabled**: When a scooter is disabled.
- **ScooterBecameRentable**: When the combination of enabled/out of service/standby is changed to make the scooter rentable.
- **ScooterBecameNotRentable**: When the combination of enabled/out of service/standby is changed to make the scooter not rentable.
