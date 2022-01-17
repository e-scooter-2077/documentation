# Analysis Process

After analyzing the domain, the second meeting with the experts focused on the expected requirements of the whole system.

This interview was conducted following the process of defining [user-stories](user-stories.md) from different perspectives in order to deduce the overall behaviour of the system from the expectations that the customer/experts had about the required functionalities. 

A Use Case UML diagram was produced from those stories, detailing how the different use cases were related to each other and to the actors of the system.

In the end, three main actors of the system were identified:

- **Customers** are the people that rent scooters through the service
- **Managers** monitor the scooters on a remote dashboard and can inspect the insights to see the suggested drop points
- **Assistants** are on the field and recover scooters that need to be recharged and drop charged scooters in the suggested drop points

Most of the use cases relate to the Customers and to the processes that need to happen before and while a customer rents a scooter. This is because the expected behavior from the customers point of view is the focus for the business success.

## Result
```plantuml
@startuml
!include requirement-analysis/use-case-diagram.puml
@enduml
```
