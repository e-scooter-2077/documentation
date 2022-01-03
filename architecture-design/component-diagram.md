# Component Diagram

```plantuml
@startuml
title Legend
!include metamodel/deployment.metamodel.iuml
$print_legend()
@enduml
```
-----------------------------------------------------------
```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml

$subdomain "E-Scooter Subdomain" {

    $context "Scooter Data Context" {
        $microservice "Scooter Data" as data
        $exposes_topic(data, "Scooter Lifecycle", scooterLifecycle)
    }

    $context "Scooter Monitor & Control Context" {
        $azureiothub "Scooter IoT Hub" as scooterhub

        $function "Manage Devices" as deviceManager
        $observes(deviceManager, scooterLifecycle)
        $updates(deviceManager, scooterhub)

        $device "Scooter" as scooter
        $observes(scooter, scooterhub)
        $updates(scooter, scooterhub)

        $microservice "Scooter Monitor" as monitor
        $exposes_topic(monitor, "Scooter Status", scooterStatus)
        $observes(monitor, scooterLifecycle)

        $function "Manage Telemetry" as telemetryManager
        $observes(telemetryManager, scooterhub)
        $updates(telemetryManager, monitor)
        $function "Manage Reported Properties" as reportedManager
        $observes(reportedManager, scooterhub)
        $updates(reportedManager, monitor)

        $function "Scooter Control" as control
        $observes(control, scooterStatus)
        $updates(control, scooterhub)
        $function "Manage Controls" as controlsManager
        $observes(controlsManager, control)
    }

    $context "Area of Service Context" {
        $microservice "Area of Service" as area
        $observes(area, scooterLifecycle)
        $observes(area, scooterStatus)
    }



}

$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice Customer as customer
        $exposes_topic(customer, Customer Lifecycle, customerLifecycle)
    }
    $context "Authentication Context" {
        $microservice Authentication as auth
    }
    $observes(auth, customerLifecycle)
    '$customer_supplier(auth, customer)
}

$subdomain "Payment Subdomain" {
    $context "Payment Context" {
        $microservice "Payment" as payment
        $microservice "Legacy Payment" as paymentLegacy
        $updates(payment, paymentLegacy)
        $observes(payment, customerLifecycle)
    }
}

$subdomain "Rent Subdomain" {

    $context "Rent Context" {
        $microservice "Rent" as rent
        $observes(rent, scooterLifecycle)
        $observes(rent, scooterStatus)
        $observes(rent, area)
        $sends_commands(rent, control)
        $exposes_topic(rent, Rent Lifecycle, rentLifecycle)
        $function "Manage Rents" as rentManager
        $observes(rentManager, rentLifecycle)
        $function "Manage Scooter Availability" as availabilityManager
        $observes(availabilityManager, rent)
    }

    $context "Trip Context" {
        $microservice "Trip" as trip
        $observes(trip, rentLifecycle)
        $observes(trip, scooterStatus)
    }
    
    $context "Rent Payment Context" {
        $microservice "Rent Payment" as rentPayment
        $observes(rentPayment, rentLifecycle)
        $observes(rent, rentPayment)
        $updates(rentPayment, payment)
    }
}

$subdomain "Insight Subdomain" {
    $context "Drop Points Planning Context" {
        $microservice "Drop Points Planning" as dpPlanning
        $observes(dpPlanning, trip)
        $observes(dpPlanning, area)
    }
}

@enduml
```

```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml
$azuredt "Scooter Digital Twins" as twins
$microservice "Scooter DT Gateway" as twinsGateway
$frontend "Mobile App" as app
$queries(app, twinsGateway)
$queries(twinsGateway, twins)
$frontend "Administrator Application" as adminApp
$queries(adminApp, twins)

$function "Manage Controls" as controlsManager
$updates(controlsManager, twins)

$function "Manage Telemetry" as telemetryManager
$updates(telemetryManager, twins)

$function "Manage Devices" as deviceManager
$updates(deviceManager, twins)

$function "Rent Manager" as rentManager
$updates(rentManager, twins)
@enduml
```
