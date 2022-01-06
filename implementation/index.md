# Implementation

This section explains how the different parts of the system are organized and implemented.

## Scooter Service Backend

The backend is fully deployed on Microsoft Azure: each resource is under the `e-scooter` resource group.

The next sections describe the different requirements of the system and how they were realized using resources deployed on the Azure Cloud.

### Control of the Physical devices
The connection with the physical devices is managed through an *IoT Hub* named `scooter-iot-hub`.
<!-- add something about the emulator? -->

### Data aggregation
The layer that aggregates the data from all the different sections of the system is implemented making use of *Azure Digital Twins* and is named `scooter-digital-twins`. This layer stores the latest updated state of the whole system keeping data about customers, scooters and rents in a comprehensive view and can be used as an eventual consistent source to read data instead of interrogating each microservice.

The result structure is a Digital Twin Graph as provided by the azure tool that allow to store current state data about the twins and relationships between each other. [Here](digital-twins-models.md) are the models used on the graph for Scooters and Customers twins.

### Inter-Service communication
Communication among services is carried out in an asynchronous fashion using events queues.

- Events are carried around between services using a *Service Bus* named `scooter-event-bus` on the topic `production/service-events`. 
- Each subscriber to the `scooter-event-bus` manages its own subscription filtering only the relevat events to receive.
- Telemetry events from the devices is transmitted through an *Event Grid* from the IoTHub.

### Event Handling
Azure functions are generally used as event handlers to map the communication from a service to another and especially when dealing with updating the Digital Twin Layer.

Here is a list of all the implemented Azure functions:

- [manage devices](https://github.com/e-scooter-2077/scooter-physical-control.manage-devices) is a function that manages the creation and destruction of scooters on the IoTHub and on the Digital Twin Layer.
- [manage customer](https://github.com/e-scooter-2077/customer.manage-customers) is a function that manages the creation and destruction of the customers' digital twins.
- [manage rents](https://github.com/e-scooter-2077/rent.manage-rents) is a function that manages the creation and destruction of renting relationships on the digital twin graph.
- [manage telemetry updates](https://github.com/e-scooter-2077/scooter-monitor.manage-telemetry-updates) updates the state of each scooter digital twin when new telemetry is received by the IoTHub (e.g. position, speed).
- [manage reported properties](https://github.com/e-scooter-2077/scooter-monitor.manage-reported-properties) updates the state of each scooter digital twin when reported properties are updated on the device (e.g. lock state, standby).
- [manage scooter availability](https://github.com/e-scooter-2077/rent.manage-scooter-availability) update the state of each scooter digital twin when the availability is changed from an administrator of the system on the rent service.

Two functions instead of managing the state of the Digital Twin Graph are extending the functionalities of the IoTHub to create the equivalent of the Scooter Control & Monitor Context: 
- [scooter control](https://github.com/e-scooter-2077/scooter-control) ....<!-- add here description of what it does-->
- [scooter monitor](https://github.com/e-scooter-2077/scooter-monitor) ....<!-- add here description of what it does-->

Functions were also used to implement mocked parts of the system when needed. For example the [rent payment](https://github.com/e-scooter-2077/rent-payment.mock) which is a mock implementation of the Rent Payment context that always confirm the rents.

### Microservices
Microservices are implemented with an *App Service*. We fully developed as a standalone microservice only the [Rent service](https://github.com/e-scooter-2077/rent-service) that maps the Rent Context.
<!--add something here to explain how the rent service is implemented maybe-->


## Frontend

### [Admin frontend](https://github.com/e-scooter-2077/admin-frontend)

The Admin frontend is implemented using Angular: polling the Digital Twin Layer, it shows the scooters in a 2D map with an icon colored depending on the state of each scooter:

- Green when it's free;
- Red when it's currently rented;
- Yellow when it is in standby;
- Grey when it is disabled.
  
Clicking on a scooter it is possible to check the value of its properties and enable or disable it to allow or prevent renting.
This features are implemented using Angular services.

### [Customer frontend](https://github.com/e-scooter-2077/customer-frontend)

The Customer frontend is implemented in C# using WPF: it is a very simple mock of the mobile app that users could use to rent an available scooter. 

### [Scooter Data mock](https://github.com/e-scooter-2077/scooter-data.mock)

The Scooter Data Mock is a mock implementation with WPF of the Scooter Data context. It allows to create a new scooter identity or to remove an existing one by generating the corresponding events which are then handled by the rest of the system accordingly. It does not store any data about the scooter since this was not relevant for the other core services.

### [Customer mock](https://github.com/e-scooter-2077/customer.mock)
The Customer Mock is a mock implementation with WPF of the Customer context. It allows to create a new customer identity or to remove an existing one by generating the corresponding events which are then handled by the rest of the system accordingly. It does not store any data about the customer since this was not relevant for the other core services.

## [Device emulator](https://github.com/e-scooter-2077/device-emulator)

The Device emulator is the C# mock implementation of the usage of each scooter: it moves the scooters, uses the battery and set standby mode.
