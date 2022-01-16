# Rent Payment Domain Model

## Class Diagram
```plantuml
@startuml
!include metamodel/domain.entities.metamodel.iuml

$aggregate("Rent") {
  $aggregate_root("Rent", rent) {
    + id: EntityId
    + start: Timestamp
    + lastPaymentTime: Timestamp
    + rider: EntityId
    + stopped: Boolean
  }
}

$value("Currency", currency) {
  + amount: Integer
}

$service("RentPaymentManager", rentpay) {
  + onRentStarted(rentId: EntityId, rider: EntityId): Result[Rent]
  + onRentStopped(rent: Rent): Result[Nothing]
  + onNextPayment(rent: Rent): Result[Nothing]
}

rentpay ..> currency
rentpay .> rent

@enduml
```

## Domain Events

- **RentPaymentAuthorized**: when a customer has enough credit to pay the unlocking fee.
- **RentPaymentRejected**:  when a customer does not have enough credit to pay the unlocking fee.
- **CreditExhaustedForRent**: when the credit of a customer is not enough to pay the upkeep cost while riding a scooter for a rent.
 