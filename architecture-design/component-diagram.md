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
$azuredt "Scooter Digital Twins" as twins

$subdomain "E-Scooter Subdomain" {
    $context "Scooter Data Context" {
        $microservice "Scooter Data (Mock)" as data
        $exposes_topic(data, "Scooter Lifecycle", scooterLifecycle)
    }

    $context "Scooter Monitor & Control Context" {
        $azureiothub "Scooter IoT Hub" as scooterhub

        $function "Manage Devices" as deviceManager
        $observes(deviceManager, scooterLifecycle)
        $updates(deviceManager, twins)
        $updates(deviceManager, scooterhub)

        $device "Scooter" as scooter
        $observes(scooter, scooterhub)
        $updates(scooter, scooterhub)

        $function "Scooter Monitor" as monitor
        $exposes_topic(monitor, "Scooter Status", scooterStatus)
        $observes(monitor, scooterhub)

        $function "Manage Telemetry" as telemetryManager
        $observes(telemetryManager, scooterhub)
        $updates(telemetryManager, twins)

        $function "Manage Reported Properties" as reportedManager
        $observes(reportedManager, monitor)
        $updates(reportedManager, twins)

        $function "Scooter Control" as control
        $updates(control, scooterhub)
    }
}

$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice "Customer (Mock)" as customer
        $exposes_topic(customer, Customer Lifecycle, customerLifecycle)
        
        $function "Manage Customers" as customerManager
        $observes(customerManager, customerLifecycle)
        $updates(customerManager, twins)
    }
}

$subdomain "Rent Subdomain" {
    $context "Rent Payment Context" {
        $microservice "Rent Payment (Mock)" as rentPayment
        $exposes_topic(rentPayment, Rent Payment Events, rentPaymentEvents)
    }

    $context "Rent Context" {
        $microservice "Rent" as rent
        $exposes_topic(rent, Rent Lifecycle, rentLifecycle)
        $exposes_topic(rent, Scooter Enabling, scooterEnabling)
        $observes(rent, scooterLifecycle)
        $observes(rent, scooterStatus)
        $observes(rent, customerLifecycle)
        $observes(rent, rentPaymentEvents)
        $sends_commands(rent, control)

        $function "Manage Scooter Enabling" as availabilityManager
        $observes(availabilityManager, scooterEnabling)
        $updates(availabilityManager, twins)

        $function "Manage Rents" as rentManager
        $observes(rentManager, rentLifecycle)
        $updates(rentManager, twins)
        
        $observes(rentPayment, rentLifecycle)
    }
}

@enduml
```
