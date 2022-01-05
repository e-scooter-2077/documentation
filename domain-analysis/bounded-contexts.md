# Bounded Contexts

### E-Scooter Subdomain
*Core Subdomain*
#### Scooter Monitor Context
One of the core contexts. Responsible for keeping track of scooter positions, battery level and other useful data coming from the physical devices. 
#### Scooter Control Context
Responsible for keeping track of the scooter logic state, meaning managing the properties of being locked/unlocked and enabled/disabled. Provides all the operations on the scooter state to other contexts and it's considered the ground truth on this data. Also responsible for defining control policies and apply them operating on the Physical Control, like power save, max speed, that depend only on the scooter physical state.
#### Scooter Physical Control Context
Responsible for physical actuation of all the remote operations on the scooter.
#### Area of Service Context
Keeps track of service areas, scooter-area bindings and scooter position in order to detect escapes.
#### Scooter Data Context
Storage of e-scooters technical and logical static information, like ID, serial number, dimensions, weight...

### Rent Subdomain
*Core Subdomain*
#### Rent Context
Manages the renting operations including storing data on which scooters are rented and by whom. Provides the interface for searching and renting scooters and communicates with the State Context in order to update the state when needed.
#### Trip Context
Data collection about trips done by all customers such as starting points, duration, kilometers.
#### Rent Payment Context
Responsible for managing the payment policy for customer rents.

### Insight Subdomain
*Core Subdomain*
#### Drop points Planning Context
Exploits usage data from other contexts to compute drop point locations or suggestions.
Stores data about user search requests coming to the Rent Context.

### Payment Subdomain
*Supporting Subdomain*
#### Payment Context
Manages all customer in-app transactions, virtual currency, and triggers real-world transactions.

### User Subdomain
*Generic Subdomain*
#### Customer Context
Keeps track of customers anagraphic data.
#### Authentication Context
Manages login data and role based authentication for all agents, including employees and customers.

