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

## Details


### Rent Process Diagram
```plantuml
@startuml
participant "Customer" as customer
participant "Scooter Control Service" as control
participant "Rent Service" as rent
participant "Rent Payment Service" as rent_payment
participant "Payment Service" as payment

customer -> rent : request rent

rent -> rent : create pending rent
return Rent

rent ->> rent_payment : rentRequested(customerId, rentId)

rent --> customer : OK

rent_payment -> payment : charge unlock price + first x minutes
return OK

rent_payment ->> rent : rentAuthorized(rentId, startTime)

rent ->> customer : rentConfirmed(rentId)

rent ->> control : unlockScooter(scooterId)

loop every x minutes while credit is sufficient and rent is not stopped
  rent_payment -> payment : charge x minutes price
  alt
    payment --> rent_payment : OK
  else
    payment --> rent_payment : Insufficient credit
    rent_payment ->> rent : creditExhaustedForRent(rentId, endTime)

    rent ->> control : lockScooter(scooterId)
  end
end

customer -> rent : stop rent

rent ->> rent_payment : rentStopped(rentId)

rent ->> control : lockScooter(scooterId)

@enduml
```

## Domain Events

- **RentAuthorized**: 
- **RentNotAuthorized**: 
- **CreditExhaustedForRent**: 
 