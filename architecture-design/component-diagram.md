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
!include metamodel/components.metamodel.iuml

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
!include metamodel/components.metamodel.iuml

$subdomain "E-Scooter Subdomain" {
    $device "Scooter (Emulated)" as scooter

    $context "Scooter Data Context" {
        $microservice "Scooter Data (Mock)" as data
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

        $exposes_topic(monitor, "Scooter Status", scooterStatus)
        $observes(monitor, properties)
        $observes(monitor, telemetries)

        $updates(control, iotHub)
    }
    $observes(scooter, desired)
    $updates(scooter, iotHub)
}

@enduml
```

This subdomain contains all the services and functions that deal with the scooter lifecycle and with the exchange of information between the physical devices and the system.

Since the **scooter data** context has the authority over the identity of the scooters and, thus, over their existence in the system, a microservice would've been an ideal implementation. However, due to the limited time, it was mocked via a gui that mimics its behavior exposing the _scooter lifecycle_ interface (i.e. notifying the creation/deletion of scooters).

On the other hand, the **Scooter Monitor & Control** context deals with the communication with the physical world. Its access point to the real world has been implemented via the **[Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/)**, a service specifically designed to be the pivot point between the cloud and IoT devices.

The IoT Hub can keep track of a large set of entities (denoted as _Devices_), each with its own identity. Each device has two sets of properties to hold state: (i) _Desired properties_, used by the cloud infrastructure to communicate the desired state for the device; (ii) _Reported properties_, used by the device to communicate its actual state (which can be different from the desired one). Furthermore, each device can emit a series of frequent events (almost a continuous stream) denoted as _Telemetry_. These are meant for continuously changing properties (like position, speed or battery level, in the e-scooter domain).

The IoT Hub can be configured to emit events whenever a telemetry is sent or whenever a reported property changes. This feature has been used to integrate it with the rest of the ecosystem. In order to expose a simpler interface to the rest of the system, the **Scooter Monitor** function has been developed to intercept events from the IoT Hub and translating them into events tailored for the e-scooter domain.

## User subdomain
```plantuml
@startuml
!include metamodel/components.metamodel.iuml

$subdomain "User Subdomain" {
    $context "Customer Context" {
        $microservice "Customer (Mock)" as customer
        $exposes_topic(customer, "Customer Lifecycle", customerLifecycle)
    }
}

@enduml
```

## Rent subdomain
```plantuml
@startuml
!include metamodel/components.metamodel.iuml

$subdomain "E-Scooter Subdomain" {
    $topic "Scooter Lifecycle" as scooterLifecycle
    $topic "Scooter Status" as scooterStatus
    $function "Scooter Control" as control
}

$subdomain "User Subdomain" {
    $topic "Customer Lifecycle" as customerLifecycle
}

$subdomain "Rent Subdomain" {
    $context "Rent Payment Context" {
        $microservice "Rent Payment (Mock)" as rentPayment
        $exposes_topic(rentPayment, "Rent Payment Events", rentPaymentEvents)
    }

    $context "Rent Context" {
        $microservice "Rent" as rent
        $exposes_topic(rent, "Rent Lifecycle", rentLifecycle)
        $exposes_topic(rent, "Scooter Enabling", scooterEnabling)
        $observes(rent, scooterLifecycle)
        $observes(rent, scooterStatus)
        $observes(rent, customerLifecycle)
        $observes(rent, rentPaymentEvents)
        $updates(rent, control)        
        $observes(rentPayment, rentLifecycle)
    }
}

@enduml
```

## Azure Digital twins
```plantuml
@startuml
!include metamodel/components.metamodel.iuml
left to right direction

$azure_service "Digital Twins" as twins

$subdomain "E-Scooter Subdomain" {
    $topic "Scooter Lifecycle" as scooterLifecycle
    $topic "Scooter Status" as scooterStatus
    $topic "Telemetry Updates" as telemetries

    $function "Manage Devices" as deviceManager
    $observes(deviceManager, scooterLifecycle)
    $updates(deviceManager, twins)

    $function "Manage Telemetry" as telemetryManager
    $observes(telemetryManager, telemetries)
    $updates(telemetryManager, twins)

    $function "Manage Reported Properties" as reportedManager
    $observes(reportedManager, scooterStatus)
    $updates(reportedManager, twins)
}

$subdomain "User Subdomain" {
    $topic "Customer Lifecycle" as customerLifecycle
    
    $function "Manage Customers" as customerManager
    $observes(customerManager, customerLifecycle)
    $updates(customerManager, twins)
}

$subdomain "Rent Subdomain" {
    $topic "Scooter Enabling" as scooterEnabling
    $topic "Rent Lifecycle" as rentLifecycle

    $function "Manage Scooter Enabling" as availabilityManager
    $observes(availabilityManager, scooterEnabling)
    $updates(availabilityManager, twins)

    $function "Manage Rents" as rentManager
    $observes(rentManager, rentLifecycle)
    $updates(rentManager, twins)
}

@enduml
```
