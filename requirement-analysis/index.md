# Analysis Process

After analyzing the domain with the experts. Another meeting was held focusing on the expected requirements of the whole system.

This interview was conducted following the process to define user-stories from different perspectives in order to deduce the overall behaviour of the system from the expectations that the customer had about the required functionalities. 

While defining the user stories a Use Case diagram was produced, detailing how the different use cases emerging from the stories were related between each other and with the actors of the system.

In the end, three main actors of the system were identified:

- **Customers** are the people that rent scooters with the service
- **Managers** monitor the scooters on a remote dashboard and can inspect the insights to see the suggested drop points
- **Assistants** are on the field and recover scooters that need to be recharged and drop charged scooters in the suggested drop points

Most of the usecases relate to the Customers and to the processes that need to happen before and while a customer rents a scooter in order to have at all times the expected behaviour of the system.

## Result
```plantuml
@startuml
!include requirement-analysis/use-case-diagram.puml
@enduml
```
