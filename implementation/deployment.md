# Deployment Diagram

```plantuml
@startuml
!include metamodel/components.metamodel.iuml
left to right direction

$azure_service "Azure Digital Twins" as twins
$function "Query Digital Twins" as query
$queries(query, twins)

$subdomain "E-Scooter Subdomain" {
    $device "Scooter Agent" as scooter

    $context "Scooter Data Context" {
        $microservice "Scooter Data" as data
        $exposes_topic(data, "Scooter Lifecycle", scooterLifecycle)
    }

    $context "Scooter Monitor & Control Context" {
        $azure_service "Scooter IoT Hub" as iotHub
        $function "Scooter Monitor" as monitor
        $function "Scooter Control" as control
        $function "Manage Devices" as deviceManager

        $exposes_topic(iotHub, "Property Updates", properties)
        $exposes_topic(iotHub, "Telemetry Updates", telemetries)
        $exposes_topic(iotHub, "Desired Properties", desired)

        $observes(deviceManager, scooterLifecycle)
        $updates(deviceManager, iotHub)
        $updates(deviceManager, twins)

        $exposes_topic(monitor, "Scooter Status", scooterStatus)
        $observes(monitor, properties)

        $updates(control, iotHub)
    }

    $context "Area of Service Context" {
        $microservice "Area of Service" as area
        $exposes_topic(area, "Area Events", areaEvents)
    }

    $observes(scooter, desired)
    $updates(scooter, iotHub)

    $observes(area, scooterLifecycle)
    $observes(area, scooterStatus)

    $function "Manage Telemetry" as telemetryManager
    $observes(telemetryManager, telemetries)
    $updates(telemetryManager, twins)

    $function "Manage Scooter Status Change" as reportedManager
    $observes(reportedManager, scooterStatus)
    $updates(reportedManager, twins)
}


$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice "Customer" as customer
        $exposes_topic(customer, "Customer Lifecycle", customerLifecycle)
    }

    $function "Manage Customers" as customerManager
    $observes(customerManager, customerLifecycle)
    $updates(customerManager, twins)
}

$subdomain "Rent Subdomain" {
    $context "Rent Payment Context" as rpc {
        $microservice "Rent Payment" as rentPayment
        $exposes_topic(rentPayment, "Rent Payment Events", rentPaymentEvents)
    }


    $context "Rent Context" as rc{
        $microservice "Rent" as rent
        $exposes_topic(rent, "Rent Lifecycle", rentLifecycle)
        $exposes_topic(rent, "Scooter Enabling", scooterEnabling)
        $observes(rent, scooterLifecycle)
        $observes(rent, scooterStatus)
        $observes(rent, customerLifecycle)
        $observes(rent, rentPaymentEvents)
        $observes(rentPayment, rentLifecycle)
    }

    $function "Manage Scooter Enabling" as availabilityManager
    $observes(availabilityManager, scooterEnabling)
    $updates(availabilityManager, twins)

    $function "Manage Rents" as rentManager
    $observes(rentManager, rentLifecycle)
    $updates(rentManager, twins)
}

$sends_commands(rent, control)
$observes(rent, areaEvents)


$client "Supervisor" as supervisor_app
$queries(supervisor_app, query)
$queries(supervisor_app, area)
$updates(supervisor_app, rent)

$client "Customer" as customer_app
$queries_and_updates(customer_app, rent)
$queries_and_updates(customer_app, customer)
$queries(customer_app, area)
@enduml
```