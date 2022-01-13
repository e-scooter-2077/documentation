# Service Architecture

## Clean architecture

Each service of the system follows a strict architecture that splits it into a series of layers, following the principles of the [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html).
Since there are several different opinions and views on what the clean architecture should look like, the team came up with its own interpretation of the layers contained in each service and of their relationships with each other.
While the details of each layer are discussed in the next sections, the diagram below shows a holistic view the resulting design.

```plantuml
@startuml
!include metamodel/architecture.metamodel.iuml
$microservice "Microservice" {
    $layer "Web" as web {
        $module "REST API"
        $module "DTO"
        $module "Authentication"
    }
    $layer "Infrastructure" as infrastructure {
        $module "Persistence"
        $module "Utility Services"
        $module "External Communication"
    }
    $layer "Application" as application {
        $module "Use cases"
        $module "Handlers"
        $module "Infrastructure Interfaces"
        $module "Authorization"
        $module "External Events"
    }
    $layer "Domain" as domain {
        $module "Entities"
        $module "Aggregates"
        $module "Value Objects"
        $module "Domain Services"
        $module "Domain Events"
    }
    web ..> infrastructure
    web ..> application
    application ..> domain
    infrastructure .> application
}

@enduml
```

### Domain Layer
This is the core layer of any service and it is responsible of defining the business rules of the context containing the service. It contains the definition of the **entities** and **value objects** belonging to the service and their associated behaviors. Entities and value objects are then organized into **aggregates** enforcing consistency boundaries over them. This layer can also define high level processes or constraints that are encapsulated inside **domain services**. Entities and domain services can notify the occurrence of any relevant state change using **domain events**, which are defined here but handled within the [application layer](#application-layer).

### Application Layer
Defines a collection of **use cases** exposed by the service and the actions to be executed for each of those through a series of **handlers** that interact with the [domain](#domain-layer) and [infrastructure](#infastructure-layer) layers to carry on those actions. Handlers can also be used to run business logic when events occur in the domain or to integrate with other services/contexts using **external events**. External events are a projection of domain events that is propagated to other contexts, useful to hide inner domain details, therefore making contexts less coupled. Furthermore, this layer is responsible of **authorization**, transactional data access and communication with external services. To keep the application logic as clean as possible, infrastructural concerns like databases, event queues or protocols are kept away from the application layer through **infrastructure interfaces** declared here, but implemented by the infrastructure layer itself.

### Infrastructure Layer
This layer holds the concrete implementation of the interfaces declared by the [Application Layer](#application-layer) or by the [Domain Layer](#domain-layer). These interfaces typically model behavior related to **Persistence**, **External Communication** and other **Utility Services** required by other layers.

### Web Layer
Implements the entry point that clients of the service use to make requests. Clients can make requests using the **REST API** exposed by each microservice, therefore this layer defines the REST endpoints that the service supports along with their interface. To that purpose, each service contains a series of **DTOs** (Data Transfer Objects, i.e. the objects modeling the format of communication with clients) to formally represent the interface of each endpoint. Lastly, this layer is responsible of managing the **Authentication** process to determine the identity of the agent making requests to the service.
