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
!include Metamodel/Deployment.metamodel.iuml

$azuredt "Scooter Digital Twins" as twins
$microservice "Scooter DT Gateway" as twinsGateway
$frontend "Mobile App" as app
$queries(app, twinsGateway)
$queries(twinsGateway, twins)
$frontend "Administrator Application" as adminApp
$queries(adminApp, twins)

$subdomain "E-Scooter Subdomain" {

    $microservice "Scooter Data" as data
    $exposes_interface(data, "Scooter Lifecycle", scooterLifecycle)


    $microservice "Scooter Physical Control" as physical
    $azureiothub "Scooter IoT Hub" as scooterhub
    $function "Manage Devices" as deviceManager
    $listens(deviceManager, data)
    $listens(deviceManager, scooterLifecycle)
    $updates(deviceManager, scooterhub)
    $updates(deviceManager, twins)

    $updates(physical, scooterhub)
    $listens(physical, scooterLifecycle)

    $device "Scooter" as scooter
    $listens(scooter, scooterhub)
    $updates(scooter, scooterhub)

    $microservice "Scooter Monitor" as monitor
    $exposes_interface(monitor, "Scooter Positions", scooterPositions)
    $function "Manage Telemetry" as telemetryManager
    $listens(monitor, scooterLifecycle)
    $listens(telemetryManager, scooterhub)
    $updates(telemetryManager, monitor)
    $updates(telemetryManager, twins)

    $microservice "Area of Service" as area
    $listens(area, scooterLifecycle)
    $listens(area, scooterPositions)
    
    $microservice "Scooter Control" as control
    $listens(control, area)
    $listens(control, scooterLifecycle)
    $listens(control, scooterPositions)
    $updates(control, physical)
    $function "Manage Controls" as controlsManager
    $listens(controlsManager, control)
    $updates(controlsManager, twins)


    '$conformist(area, lifecycle, $interface=true)

    '$common_interface(monitor, Scooter Positions, positions)
    '$conformist(area, positions, $interface=true)

    '$customer_supplier(physical, control)
    '$shared_kernel(monitor, physical, Azure IoT Hub)
    '$conformist(control, monitor)
}

' $subdomain "Rent Subdomain" {
'     $context "Rent Context" as rent
'     $context "Trip Context" as trip
'     $context "Rent Payment Context" as rent_payment

'     $conformist(trip, rent)
'     $conformist(rent_payment, rent)
'     $conformist(rent, rent_payment)
' }

' $conformist(rent, lifecycle, $interface=true)
' $bidirectional_customer_supplier(control, rent)

' $customer_supplier(control, area)
' $conformist(rent, positions, $interface=true)
' $conformist(trip, positions, $interface=true)

' $subdomain "Insight Subdomain" {
'     $context "Drop Points Planning Context" as planning
' }

' $conformist(planning, trip)
' $conformist(planning, rent)
' $conformist(planning, area)

' $subdomain "Payment Subdomain" {
'     $anticorruption_layer "Payment Context" as payment {
'         $context "Payment Legacy Service" as payment_legacy

'     }
' }

' $conformist(rent_payment, payment)

' $subdomain "User Subdomain" {
'     $context "Customer Context" as customer
'     $context "Authorization Context" as auth
'     note right of auth
'         Provider of global authentication medium
'     end note
'     $customer_supplier(auth, customer)
' }

' $conformist(payment, customer)
@enduml
```
