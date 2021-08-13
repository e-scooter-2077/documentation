# Implementation
## Microservices

## Microsoft Azure

#### Naming Convention

- Every resource is under the `e-scooter` group;
- The *Scooter Physical Control* IoT Hub is named `scooter-iot-hub`
- The *Scooter Monitor* Digital Twin is named `scooter-digital-twins
- The Scooter Event Bus is named `scooter-event-bus`
    - The topic used for events generated from any microservice is named `service-events`
    - Every subscription name is in [kebab case](https://en.wikipedia.org/wiki/Letter_case#Special_case_styles)
    and should be named after the subscriber name inside Azure
- Azure Functions should be named after verbs and in kebab case
- The Scooter Event Grid is named `scooter-event-grid`

