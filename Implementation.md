# Implementation

In this section is explained how are organized and implemented the parts of the system.

## Backend - Microsoft Azure

Backend is fully deployed on Microsoft Azure: each resource is under the `e-scooter` group.

### Naming Convention

The usual naming convention used under this group is [kebab case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles)

- The *Scooter Physical Control* IoT Hub is named `scooter-iot-hub`
- The Digital Twin Layer is named `scooter-digital-twins`
- The Scooter Event Grid is named `device-events`
- The Scooter Event Bus is named `scooter-event-bus`
    - The topic used for events generated from any microservice is named `production/service-events`
    - Every subscription name should be named after the subscriber name inside Azure
- Azure Functions should be named with the Domain model and the specific usage, if exists; `manage` represents functions that update the Digital Twin.
- Microservice should be named with the Domain model followed by `-service`

### Functions

Our functions are the link between different services:

- [scooter control](https://github.com/e-scooter-2077/scooter-control) even if is implemented as an Azure function it's logically a service infact it extends the functionality of the IotHub
- [scooter monitor](https://github.com/e-scooter-2077/scooter-monitor)
- [rent payment](https://github.com/e-scooter-2077/rent-payment.mock) (mock)
- [manage customer](https://github.com/e-scooter-2077/customer.manage-customers)
- [manage rents](https://github.com/e-scooter-2077/rent.manage-rents)
- [manage telemetry updates](https://github.com/e-scooter-2077/scooter-monitor.manage-telemetry-updates)
- [manage devices](https://github.com/e-scooter-2077/scooter-physical-control.manage-devices)
- [manage reported properties](https://github.com/e-scooter-2077/scooter-monitor.manage-reported-properties)
- [manage scooter availability](https://github.com/e-scooter-2077/rent.manage-scooter-availability)

### Microservice

Our services differ from functions because they've a status, usually linked with a Database:

- [Rent service](https://github.com/e-scooter-2077/rent-service)

## Frontend

### [Admin frontend](https://github.com/e-scooter-2077/admin-frontend)

The Admin frontend is implemented in angular: it shows the scooters in a 2D map that updates polling the Digital Twin Layer. Clicking on a scooter is possible to check the its status and allow to enable or disable it.

### [Customer frontend](https://github.com/e-scooter-2077/customer-frontend)

The Customer frontend is implemented in C# using Windows Forms: it allows to rent an available scooter from a list.

### [Scooter data](https://github.com/e-scooter-2077/scooter-data.mock)

The Scooter data is a mock implementation of the real creation of a scooter: it enable to insert or remove a new scooter in the system.

## [Device emulator](https://github.com/e-scooter-2077/device-emulator)
The Device emulator is the C# mock implementation of the usage of each scooter: it moves the scooters, use the battery.