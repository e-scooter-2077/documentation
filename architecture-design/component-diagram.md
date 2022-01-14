# Component Diagram

This section contains an overview of the final version of the system as it's been implemented, giving an holistic view of the developed components and their relationships with each other.

Components of the system can be categorized in:

- **Microservices**: standalone services that encapsulate their state and provide a well defined set of functionalities accessible through their public interface. Microservices can communicate with each other through events or asynchronous commands.
- **Azure services**: instances of services offered by the _Microsoft Azure_ cloud infrastructure, providing a wide range of features that need to be integrated with other services.
- **Functions**: stateless instances that are typically used to translate and facilitate communication among other components of the system. They fit particularly well in the _serverless_ computation model, and are therefore implemented through _Azure Functions_.
- **Devices**: computational resources belonging to the physical world.

The diagrams in the sections below show the communication between each pair of components, classifying it in:

- **Synchronous updates (_Updates_ arrow)**: commands sent over synchronous media, like HTTP or RPC.
- **Asynchronous commands (_Sends commands_ arrow)**: commands sent in an asynchronous way, typically using message queues.
- **Publish subscribe (_Exposes topic_ & _Observes_ arrows)**: events are published on topics and observed by subscribers of that topic, using asynchronous messaging.

```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml

title Legend
left to right direction
interface "Topic Name" as topic
label F
label E
label D
label C
label B
label A

$updates(A, B) : Updates
$sends_commands(C, D) : Sends commands
$exposes_topic(E, Topic Name, _) : Exposes topic
$observes(F, topic) : Observes

$subdomain "Subdomain" {
    $context "Context" {
        $microservice "Microservice"
        $function "Function"
        $azure_service "Azure Service"
        $device "Device"
    }
}
@enduml
```

## E-Scooter subdomain
```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml

$azure_service "Digital Twins" as twins

$subdomain "E-Scooter Subdomain" {
    $context "Scooter Data Context" {
        $microservice "Scooter Data (Mock)" as data
        $exposes_topic(data, "Scooter Lifecycle", scooterLifecycle)
    }

    $context "Scooter Monitor & Control Context" {
        $azure_service "Scooter IoT Hub" as iotHub
        $exposes_topic(iotHub, "Property Updates", properties)
        $exposes_topic(iotHub, "Telemetry Updates", telemetries)
        $exposes_topic(iotHub, "Desired Properties", desired)

        $function "Manage Devices" as deviceManager
        $observes(deviceManager, scooterLifecycle)
        $updates(deviceManager, twins)
        $updates(deviceManager, iotHub)

        $function "Scooter Monitor" as monitor
        $exposes_topic(monitor, "Scooter Status", scooterStatus)
        $observes(monitor, properties)
        $observes(monitor, telemetries)

        $function "Manage Telemetry" as telemetryManager
        $observes(telemetryManager, telemetries)
        $updates(telemetryManager, twins)

        $function "Manage Reported Properties" as reportedManager
        $observes(reportedManager, scooterStatus)
        $updates(reportedManager, twins)

        $function "Scooter Control" as control
        $updates(control, iotHub)
    }

    $device "Scooter (Emulated)" as scooter
    $observes(scooter, desired)
    $updates(scooter, iotHub)
}

@enduml
```

## User subdomain
```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml

$azure_service "Digital Twins" as twins

$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice "Customer (Mock)" as customer
        $exposes_topic(customer, Customer Lifecycle, customerLifecycle)
        
        $function "Manage Customers" as customerManager
        $observes(customerManager, customerLifecycle)
        $updates(customerManager, twins)
    }
}

@enduml
```

## Rent subdomain
```plantuml
@startuml
!include metamodel/deployment.metamodel.iuml

$azure_service "Digital Twins" as twins

$subdomain "E-Scooter Subdomain" {
    $context "Scooter Data Context" {
        $microservice "Scooter Data (Mock)" as data
        $exposes_topic(data, "Scooter Lifecycle", scooterLifecycle)
    }

    $context "Scooter Monitor & Control Context" {
        $function "Scooter Monitor" as monitor
        $exposes_topic(monitor, "Scooter Status", scooterStatus)

        $function "Scooter Control" as control
    }
}

$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice "Customer (Mock)" as customer
        $exposes_topic(customer, Customer Lifecycle, customerLifecycle)
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
        $updates(rent, control)

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