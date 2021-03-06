# Implementation

This section explains how the different parts of the system are organized and implemented.

The choice was to carry out the full analysis of a complex domain to be able to approach a "real-world" problem in its entirety and use the Domain Driven Design techniques to study it, but as far as the implementation goes, we always knew that it was not needed to implement the whole system, so we decided to focus on the core and interesting aspects, mock the supporting services and completly ignore some of the features that were outside of this core prototype boundary scope.

In the end, the most interesting part was the implementation of the Scooter Monitor & Control context in order to realize the Digital Twin layer that was at the core of the Pervasive Computer course for which this project will be submitted too. This meant that the Scooter Data context was mocked since it was just a collection of data relevant for the domain but not too much for the functionalities of the system.

Renting operations in the Rent Context were implemented to have at least one standalone microservice in place to challenge ourselves with the requirements of the clean architecture.
The implementation of the Area of Service context was put aside in order to focus on the most relevant use-case of the system itself which was the scooter rent management. In an iterative development process of the real system this would have probably been the smartest choice to, at least, guarantee the basic functionalities immediatly and later add the other planned features.

With the same reasoning the implementation of the Insights subdomain and the connected Trip Context were skipped and the Rent Payment context was mocked since we didn't need to manage the virtual currency and the relationship with the Payment subdomain.

The User subdomain was plain both from a design and implementative perspective so the group didn't focus too much on that. Of course in a real-world scenario authorization and security should have been put in place since the very beginning, but for a proof-of-concept like the one that was developed this was definetly not needed nor particularly interesting.

## Scooter Service Backend

The backend is fully deployed on Microsoft Azure: each resource is under the `e-scooter` resource group.

The next sections describe the different requirements of the system and how they were realized using resources deployed on the Azure Cloud.

### Communication with the Physical devices
The connection with the physical devices is managed through an *IoT Hub* named `scooter-iot-hub`. The IoT Hub manages devices representing them with a set of desired and reported properties and some metadata describing the device itself. Services can edit desired properties of the digital device which are pushed as events on the physical device itself and can trigger changes in the device status. Notifications on status changes can be achieved either from the use of reported properties when changes are not frequent or through telemetry events that are usually proactive streams of data coming from the physical device's sensors.

### Data aggregation
The layer that aggregates the data from all the different sections of the system is implemented making use of *Azure Digital Twins* and is named `scooter-digital-twins`. This layer stores the latest updated state of the whole system keeping data about customers, scooters and rents in an eventually consistent source. This means that the Digital Twin layer offers a comprehensive view of the domain to query the state of the system instead of interrogating each microservice separately.

The result structure is a Digital Twin Graph as provided by the Azure tool that allow to store current state of the twins and relationships between each other. [Here](digital-twins-models.md) are the models used on the graph for Scooters and Customers twins which are the main entities modelled.

### Inter-Service communication
Communication among services is carried out in an asynchronous fashion using events queues.

- Events are carried around between services using a *Service Bus* named `scooter-event-bus` on the topic `production/service-events`. 
- Each subscriber to the `scooter-event-bus` manages its own subscription filtering only the relevat events to receive.
- Telemetry events from the devices is transmitted through an *Event Grid* from the IoTHub.

### Event Handling
Azure functions are generally used as event handlers to map the communication from a service to another and especially when dealing with updating the Digital Twin Layer.

Here is a list of all the implemented Azure functions:

- [manage devices](https://github.com/e-scooter-2077/scooter-physical-control.manage-devices) is a function that manages the creation and removal of scooters on the IoTHub and on the Digital Twin Layer.
- [manage customer](https://github.com/e-scooter-2077/customer.manage-customers) is a function that manages the creation and removal of the customers' digital twins.
- [manage rents](https://github.com/e-scooter-2077/rent.manage-rents) is a function that manages the creation and removal of renting relationships on the digital twin graph.
- [manage telemetry updates](https://github.com/e-scooter-2077/scooter-monitor.manage-telemetry-updates) updates the state of each scooter digital twin when new telemetry is received by the IoTHub (e.g. position, speed).
- [manage reported properties](https://github.com/e-scooter-2077/scooter-monitor.manage-reported-properties) updates the state of each scooter digital twin when reported properties are updated on the device (e.g. lock state, standby).
- [manage scooter availability](https://github.com/e-scooter-2077/rent.manage-scooter-availability) update the state of each scooter digital twin when the availability is changed from an administrator of the system on the rent service.

Two functions instead of managing the state of the Digital Twin Graph are extending the functionalities of the IoTHub to implement the Scooter Control & Monitor Context: 

- [scooter control](https://github.com/e-scooter-2077/scooter-control) is a function that exposes the default applicable actions on the devices: in particular, it manages the lock/unlock command and listens to the telemetries from the *IoTHub* to apply domain policies (e.g. modifying max speed according to battery level).
- [scooter monitor](https://github.com/e-scooter-2077/scooter-monitor) is a function that exposes the events concerning changes of the device state (e.g. lock, standby)

Functions were also used to implement mocked parts of the system when needed. For example the [rent payment](https://github.com/e-scooter-2077/rent-payment.mock) which is a mock implementation of the Rent Payment context that always confirm the rents.

### Microservices
Microservices are implemented with an *App Service*. Only the [Rent service](https://github.com/e-scooter-2077/rent-service) was developed as a fully standalone microservice that maps the Rent Context.

## Frontend

### [Admin frontend](https://github.com/e-scooter-2077/admin-frontend)

The Admin frontend is implemented using Angular: polling the Digital Twin Layer, it shows the scooters in a 2D map with an icon colored depending on the state of each scooter:

- Green when it's free;
- Red when it's currently rented;
- Yellow when it is in standby;
- Grey when it is disabled.
  
By clicking on a scooter it is possible to check the value of its properties and enable or disable it to allow or prevent renting.

### [Customer frontend](https://github.com/e-scooter-2077/customer-frontend)

The Customer frontend is implemented in C# using WPF: it is a very simple mock of the mobile app that users could use to rent an available scooter. 

### [Scooter Data mock](https://github.com/e-scooter-2077/scooter-data.mock)

The Scooter Data Mock is a mock implementation with WPF of the Scooter Data context. It allows to create a new scooter identity or to remove an existing one by generating the corresponding events which are then handled by the rest of the system accordingly. It does not store any data about the scooter since this was not relevant for the other core services.

### [Customer mock](https://github.com/e-scooter-2077/customer.mock)
The Customer Mock is a mock implementation with WPF of the Customer context. It allows to create a new customer identity or to remove an existing one by generating the corresponding events which are then handled by the rest of the system accordingly. It does not store any data about the customer since this was not relevant for the other core services.

## [Device emulator](https://github.com/e-scooter-2077/device-emulator)

The Device emulator simulates the typical behavior of e-scooters in the real world, allowing us to live-test the entire system.

Implemented as [HostedService](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-6.0&tabs=visual-studio) with C# .NET, the emulator executes a loop in which queries for new or updated scooters and simulates a random realistic usage to update them: it moves the scooters, uses the battery and sets standby mode eventually, as if humans were using them to move around Cesena.

The requirement analysis of the device emulator can be found [here](https://e-scooter-2077.github.io/documentation/implementation/device-emulator/requirements.html).
