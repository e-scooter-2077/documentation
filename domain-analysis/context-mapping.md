# Context Mapping
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
