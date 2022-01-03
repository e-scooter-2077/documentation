# Context Mapping
## Context Map
```plantuml
@startuml
!include metamodel/context-map.metamodel.iuml

$subdomain "E-Scooter Subdomain" {
    $context "Area of Service Context" as area
    $context "Scooter Monitor Context" as monitor
    $context "Scooter Control Context" as control
    $context "Scooter Physical Control Context" as physical
    $context "Scooter Data Context" as data

    $common_interface(data, Scooter Lifecycle, lifecycle)
    $conformist(control, lifecycle, $interface=true)
    $conformist(monitor, lifecycle, $interface=true)
    $conformist(physical, lifecycle, $interface=true)
    $conformist(area, lifecycle, $interface=true)

    $common_interface(monitor, Scooter Status, positions)
    $conformist(area, positions, $interface=true)

    $customer_supplier(physical, control)
    $shared_kernel(monitor, physical, Azure IoT Hub)
    $conformist(control, positions, $interface=true)
}

$subdomain "Rent Subdomain" {
    $context "Rent Context" as rent
    $context "Trip Context" as trip
    $context "Rent Payment Context" as rent_payment

    $conformist(trip, rent)
    $conformist(rent_payment, rent)
    $conformist(rent, rent_payment)
}

$conformist(rent, lifecycle, $interface=true)
$bidirectional_customer_supplier(control, rent)

$customer_supplier(control, area)
$conformist(trip, positions, $interface=true)

$subdomain "Insight Subdomain" {
    $context "Drop Points Planning Context" as planning
}

$conformist(planning, trip)
$conformist(planning, rent)
$conformist(planning, area)

$subdomain "Payment Subdomain" {
    $anticorruption_layer "Payment Context" as payment {
        $context "Payment Legacy Service" as payment_legacy

    }
}

$conformist(rent_payment, payment)

$subdomain "User Subdomain" {
    $context "Customer Context" as customer
    $context "Authentication Context" as auth
    note right of auth
        Provider of global authentication medium
    end note
    $customer_supplier(auth, customer)
}

$conformist(payment, customer)
@enduml
```
