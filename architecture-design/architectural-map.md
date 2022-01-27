# Architectural map

This section contains an overview of the final version of the system as it has been designed for implementation, giving an holistic view of the developed components and their relationships with each other.

Components of the system can be categorized in:

- **Microservices**: standalone services that encapsulate their state and provide a well defined set of functionalities accessible through their public interface. Microservices can communicate with each other through events or asynchronous commands.
- **Azure services**: instances of services offered by the _Microsoft Azure_ cloud infrastructure, providing a wide range of features that need to be integrated with other services.
- **Functions**: stateless instances that are typically used to translate and facilitate communication among other components of the system. They fit particularly well in the _serverless_ computation model, and are therefore chosen to be implemented through _Azure Functions_. An important thing to note is that in Azure, the minimal unit of deployment is a _Function App_, which can host multiple functions with different tasks that scale together. The following sections use the term "function" to indicate an Azure Function App, that can therefore contain multiple instances of serverless functions.
- **Devices**: computational resources belonging to the physical world. They are embedded IoT devices onto the electric scooters managed by the system.

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
$exposes_topic(E, Topic Name, t) : Exposes topic
F --( topic : Observes

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

        $updates(control, iotHub)
    }

    $observes(scooter, desired)
    $updates(scooter, iotHub)
}

@enduml
```

This subdomain contains all the services and functions that deal with the scooter lifecycle and with the exchange of information between the physical devices and the system.

Since the **scooter data** context has the authority over the identity of the scooters and, thus, over their existence in the system, we planned to build it as a microservice. However, for this prototypal implementation, due to the limited time, it was mocked via a GUI that mimics its behavior exposing the _scooter lifecycle_ interface (i.e. notifying the creation/deletion of scooters).

On the other hand, the **Scooter Monitor & Control** context deals with the communication with the physical world. Its access point to the real world has been chosen to be realized via the **[Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/)**, a service specifically designed to be the pivot point between the cloud and IoT devices.

The IoT Hub can keep track of a large set of entities (denoted as _Devices_), each with its own identity. Each device has two sets of properties to hold state: (i) _Desired properties_, used by the cloud infrastructure to communicate the desired state for the device; (ii) _Reported properties_, used by the device to communicate its actual state (which can be different from the desired one). Furthermore, each device can emit a series of frequent events (almost a continuous stream) denoted as _Telemetry_. These are meant for continuously changing properties (like position, speed or battery level, in the e-scooter domain).

New scooters that are registered to the system are recorded as devices inside the IoT Hub thanks to the **Manage Devices** function, relying on the _scooter lifecycle_ topic. After registration, the scooter can start to be monitored and/or controlled by the system.

The physical actuation of control policies is demanded to the **Scooter Control** function, which receives asynchronous commands from other components in order for them to be propagated to the physical devices via the IoT Hub. This commands, among others, include the operations to _lock_/_unlock_ scooters, for example when a customer rents one. 

The IoT Hub can also be configured to emit events whenever a telemetry is sent or whenever a reported property changes. This feature has been exploited to integrate it with the rest of the ecosystem. In particular, the cloud service emits events when properties like _locked_ or _standby_ change. In order to have a simpler interface for reported properties updates, the **Scooter Monitor** function has been put in between the Hub and the rest of the system, to translate them into events tailored for the e-scooter domain.

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

Being a generic subdomain, the team decided to have the User subdomain be implemented using an off-the-shelf package. Therefore, the team opted to mock the interfaces of this subdomain that were needed by other components in other subdomains, namely the _Customer lifecycle_ topic. This is the topic exposed by the designed **Customer Microservice** to let others know about customers signing up to the system or customers unregistering from it. Like the _scooter data_ microservice, this was implemented via a GUI emitting the events for other services to observe.

## Rent subdomain
```plantuml
@startuml
!include metamodel/components.metamodel.iuml
left to right direction
$subdomain "E-Scooter Subdomain" {
    $topic "Scooter Lifecycle" as scooterLifecycle
    $topic "Scooter Status" as scooterStatus
    $function "Scooter Control" as control
}

$subdomain "User Subdomain" {
    $topic "Customer Lifecycle" as customerLifecycle
}

$subdomain "Rent Subdomain" {
    $context "Rent Payment Context" as rpc {
        $microservice "Rent Payment (Mock)" as rentPayment
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
}

$sends_commands(rent, control)        

@enduml
```

The rent subdomain deals with the functionalities that allow customers to rent scooters, along with side features reserved to assistants and admins of the service. The core of this subdomain is the **Rent Microservice**, which provides clients with an interface to _start_ and _stop_ rents as a customer, or to _enable_ or _disable_ scooters as an assistant or as an admin. To work properly, it needs to know about the existing customers and scooters, which is done by subscribing to the _Scooter Lifecycle_, _Scooter Status_ and _Customer Lifecycle_ topics. This service is also responsible for the _locking_ and _unlocking_ of scooters according to the lifecycle of the related rents. Since these operations are offered by the _Scooter Control_ function, the rent service only needs to send asynchronous commands to it when necessary.

The Rent Microservice does not directly deal with the payment of the rides, but delegates this responsibility to another microservice from a different bounded context, the **Rent Payment Service**. Since payments are assumed to use _virtual currency_ managed by the system, this service needs to ensure that: (i) a renting customer has enough credit to start a rent, that is _authorizing_ or _rejecting_ the rent; (ii) a rent is stopped when a customer's credit runs out. This collaboration between the two services is carried out via an exchange of events using the _Rent Lifecycle_ and _Rent Payment Events_ topics. Due to the limited time though, the Rent Payment service was mocked with a function that always authorizes rents when they are requested.

## Azure Digital Twins
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

Since this project is about the exploration of the **digital twins** paradigm, the team decided to adopt a standard and production-ready solution to keep track of the real-time state of the system. This gives clients the possibility to query it in arbitrary ways. The solution of choice was **[Azure Digital Twins](https://docs.microsoft.com/en-us/azure/digital-twins/overview)**, which has all the required features.

Azure Digital Twins allows developers to define a set of _DTDL models_ to define real-world assets as twins along with their _properties_ and the _relationships_ they can have with other digital twins. Once the models are uploaded to the Azure Digital Twins instance, external agents can use its API and/or SDK to run CRUD operations on both digital twins and relationships, in order to control the _digital twin graph_.

??? example "EScooter DTDL Models"
    DTDL models of EScooter digital twins can be found in the [twins-models repository](https://github.com/e-scooter-2077/twins-models).

Azure Digital Twins can be easily integrated with other cloud services (including the IoT Hub) using Azure Functions. In particular, the team planned to develop a group of functions to create digital twins for the scooters and the customers managed by the rest of the system and also to keep them up to date with the real-time state.
