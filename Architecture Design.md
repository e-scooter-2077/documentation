# Architecture Design

## Microservices

### Service Architecture

```plantuml
@startuml
!include Metamodel/Architecture.metamodel.iuml
left to right direction
$microservice "Microservice" {
    $layer "API" as api {
        $module "Controllers"
        $module "DTO"
        $module "Authentication"
    }
    $layer "Infrastructure" as infrastructure {
        $module "Utility Services"
        $module "Persistence"
        $module "External Communication"
    }
    $layer "Application" as application {
        $module "Handlers"
        $module "Infrastructure Interfaces"
        $module "Authorization"
    }
    $layer "Contracts" as contracts {
        $module "External Events"
    }
    $layer "Domain" as domain {
        $module "Entities"
        $module "Domain Services"
        $module "Domain Events"
    }
}

api ..> application
infrastructure ..> application
application ..> domain
application ..> contracts
@enduml
```

#### Domain Layer
This is the core layer of any service and is responsible of defining all **Entities** belonging to the service and their associated behaviors. This layer can also define high level processes or constraints that are encapsulated inside **Domain Services**. Entities and domain services can notify the occurrence of any relevant state change using **Domain Events**.

#### Contracts Layer
Defines a set of **External Events** that are used by other microservices to react to state changes outside of their bounded context. These events are typically emitted by the [Application Layer](#application-layer) in response to internal domain events and expose a subset of the properties of the domain event itself.

#### Application Layer
Defines a collection of **Handlers** that encapsulate the use cases exposed by the service and acts as an entry point to the domain layer. This layer is also responsible of managing **Authorization**, data access, consistency and external communication by delegating to the [Infrastructure Layer](#infrastructure-layer). To keep application logic as clean as possible, this communication uses **Infrastructure Interfaces** declared by the application layer itself, but implemented in [Infrastructure](#infrastructure-layer).

#### Infrastructure Layer
This layer holds the concrete implementation of the 
technology-agnostic interfaces declared by the [Application Layer](#application-layer) or by the [Domain Layer](#domain-layer). These interfaces typically model behavior related to **Persistence**, **External Communication** and other **Utility Services** required by other layers.

#### API Layer
Represents the entry point that clients of the service use to make requests. Clients can make requests using the REST API exposed by each microservice, therefore this layer defines a set of **Controllers** that declare which endpoints are supported. In addition, this is where each service defines its **DTOs** (Data Transfer Objects, i.e. the objects modeling the format of communication with clients). Lastly, this layer is responsible of managing the **Authentication** process, delegating authorization to the [Application Layer](#application-layer).

### Inter-Service dependencies
Inter-service communication can happen with:

* Synchronous API calls from the _Infrastructure_ layer to the recipient _API_ layer
* Asynchronous Event notification received through the _Application_ layer that adheres to the sender _Contracts_ layer

```plantuml
@startuml
!include Metamodel/Architecture.metamodel.iuml
left to right direction
title "Service 2 depends on Service 1"

$microservice "Service 1" as service_1 {
    $layer "API" as api_1
    $layer "Contracts" as contracts_1
}
$microservice "Service 2" as service_2 {
    $layer "Infrastructure" as infrastructure_2
    $layer "Application" as application_2
}

application_2 ..> contracts_1
infrastructure_2 ..> api_1

@enduml
```

## Global Architecture

### Deployment Diagram

```plantuml
@startuml
title Legend
!include Metamodel/Deployment.metamodel.iuml
$print_legend()
@enduml
```
-----------------------------------------------------------
```plantuml
@startuml
!include Metamodel/Deployment.metamodel.iuml

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
!include Metamodel/Deployment.metamodel.iuml
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
