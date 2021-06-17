# Domain Contexts
## Context List
#### Scooter Monitor Context
One of the core contexts. Responsible for keeping track of scooter positions, battery level and other useful data for ma#ny business use cases.
#### Scooter Logic Control Context
Responsible for defining control policies to be applied to e-scooters according to domain/business policies.
#### Scooter Remote Control Context
Responsible for all physical actuation of logic control policies.
#### Scooter Data Context
Storage of e-scooters technical and logical static information, like ID, serial number, dimensions, weight...
#### Scooter Search Context
Uses data from the Scooter Monitor Context to provide the search functionality to customers (that are looking for available scooters to ride).
#### Area of Service Context
Keeps track of service areas, scooter-area bindings and scooter position in order to detect escapes.
#### Rent Context
Handles the process of scooter renting from the beginning to its end.
#### Trip Context
Data collection about trips done by all customers.
#### Drop points Planning Context
Exploits usage data from other contexts to compute drop point locations or suggestions.
#### Customer Context
Keeps track of customers anagraphic data.
#### Payment Context
Manages all customer in-app transactions, virtual currency, and triggers real-world transactions.
#### Auth Context
Manages login data and role based authorization for all users, including employees and customers.


## Impact Map
```plantuml
@startmindmap
* E-Scooter Service
** Scooter Monitor
*** Rent
**** Payment
**** Customer
*** Drop points Planning
**** Trip
*** Scooter Search
*** Area of Service
*** Scooter Data
*** Auth
** Scooter Logic Control
*** Scooter Remote Control
@endmindmap
```

## Context Map
