# Context Mapping
The context map below shows all the different context in the scooter service domain. 

Five subdomain are identified:
 - **E-Scooter Subdomain**: manages all the logic concerning the physical scooters
 - **Rent Subdomain**: manages all the logic concerning the rent process from the financial policies to the storage of trips' data
 - **Insight Subdomain**: concerns the elaboration of the best possible drop points for scooters to improve the quality of service
 - **Payment Subdomain**: manages the logic of real-world payments by integrating with a legacy system
 - **User Subdomain**: contains the anagraphical information of the service users and provide authentication

The relationships between context are explicitated through the representation of interfaces that are exposed in order to allow the sharing of information in the system. 

The relationships between services are basically all of type conformist since the context which exposes the interface has the truth about that data and it's responsible to decide what to share externally and how.

The payment context instead is an anti-corruption layer on top of the existing legacy system that manages real world transactions.

## Context Map
```plantuml
@startuml
!include metamodel/context-map.metamodel.iuml

$subdomain "E-Scooter Subdomain" {
    $context "Area of Service Context" as area
    $context "Scooter Control & Monitor Context" as control
    $context "Scooter Data Context" as data

    $common_interface(data, Scooter Lifecycle, lifecycle)
    $common_interface(control, Scooter Status, status)
    $common_interface(area, Area Events, area_events)
}

$subdomain "Rent Subdomain" {
    $context "Rent Context" as rent
    $context "Trip Context" as trip
    $context "Rent Payment Context" as rent_payment

    $common_interface(rent, Rent Lifecycle, rent_lifecycle)
    $common_interface(rent_payment, Rent Payment Events, rent_payment_events)
    $common_interface(trip, Trip Data, trip_data)
}

$subdomain "Insight Subdomain" {
    $context "Drop Points Planning Context" as planning
}

$subdomain "Payment Subdomain" {
    $anticorruption_layer "Payment Context" as payment {
        $context "Payment Legacy Service" as payment_legacy
    }
}

$subdomain "User Subdomain" {
    $context "Authentication Context" as auth
    note bottom of auth
        Global authentication provider 
    end note
    $context "Customer Context" as customer
}


$common_interface(customer, Customer Lifecycle, customer_lifecycle)
$common_interface(payment, Payment API, payment_api)

$conformist(control, lifecycle)
$conformist(area, lifecycle)
$conformist(area, status)

$conformist(trip, rent_lifecycle)
$conformist(rent_payment, rent_lifecycle)
$conformist(rent, rent_payment_events)
$conformist(rent_payment, payment_api)

$conformist(rent, lifecycle)
$customer_supplier(control, rent)
$conformist(rent, area_events)

$conformist(planning, trip_data)
$conformist(planning, rent_lifecycle)
$conformist(planning, area_events)

$conformist(trip, status)

$conformist(payment, customer_lifecycle)
$customer_supplier(auth, customer)
$conformist(rent, customer_lifecycle)
@enduml
```
