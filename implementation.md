# Implementation

In this section is explained how are organized and implemented the parts of the system.

## Backend - Microsoft Azure

Backend is fully deployed on Microsoft Azure: each resource is under the `e-scooter` group.

### Naming Convention

The usual naming convention used under this group is [kebab case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles).

Below the list of main resources' name deployed on Azure:

- The *Scooter Physical Control* IoT Hub is named `scooter-iot-hub`
- The Digital Twin Layer is named `scooter-digital-twins`
- The Scooter Event Grid is named `device-events`
- The Scooter Event Bus is named `scooter-event-bus`
    - The topic used for events generated from any microservice is named `production/service-events`
    - Every subscription name should be named after the subscriber name inside Azure
- Azure Functions should be named with the Domain model and the specific usage, if exists; `manage` represents functions that update the Digital Twin.
- Microservice should be named with the Domain model followed by `-service`

### Microservice

Microservices are function with a status, usually implemented with a database:

- [Rent service](https://github.com/e-scooter-2077/rent-service)

### Functions

Our functions are the link between different services:

- [scooter control](https://github.com/e-scooter-2077/scooter-control) & [scooter monitor](https://github.com/e-scooter-2077/scooter-monitor) even if they're implemented as Azure functions, their composition with IotHub (of which extends functionalities) can be considered logically a service
- [rent payment](https://github.com/e-scooter-2077/rent-payment.mock) (mock)
- [manage customer](https://github.com/e-scooter-2077/customer.manage-customers)
- [manage rents](https://github.com/e-scooter-2077/rent.manage-rents)
- [manage telemetry updates](https://github.com/e-scooter-2077/scooter-monitor.manage-telemetry-updates)
- [manage devices](https://github.com/e-scooter-2077/scooter-physical-control.manage-devices)
- [manage reported properties](https://github.com/e-scooter-2077/scooter-monitor.manage-reported-properties)
- [manage scooter availability](https://github.com/e-scooter-2077/rent.manage-scooter-availability)

## Frontend

### [Admin frontend](https://github.com/e-scooter-2077/admin-frontend)

The Admin frontend is implemented in angular: polling the Digital Twin Layer, it shows the scooters in a 2D map.
Clicking on a scooter is possible to check the its status and allow to enable or disable it.
This features are implemented using angular services.

### [Customer frontend](https://github.com/e-scooter-2077/customer-frontend)

The Customer frontend is implemented in C# using WPF: it allows to rent an available scooter from a list.

### [Scooter data](https://github.com/e-scooter-2077/scooter-data.mock)

The Scooter data is a mock implementation of the real creation of a scooter: it enable to insert or remove a new scooter in the system.

## [Device emulator](https://github.com/e-scooter-2077/device-emulator)

The Device emulator is the C# mock implementation of the usage of each scooter: it moves the scooters and uses the battery.
