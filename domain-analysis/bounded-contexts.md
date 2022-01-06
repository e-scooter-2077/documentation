# Bounded Contexts
After understanding the domain with the domain experts the team proceeded with the identification of the different subdomains and the bounded contexts within it.

The analysis brought to the definition of the following contexts that were later analyzed to generate the (context map)[context-mapping.md] in order to understand the relationships between each context and move towards a definition of the different software components that define the architecture of the whole system. 

### E-Scooter Subdomain
*Core Subdomain*
#### Scooter Control & Monitor Context
One of the core contexts. Responsible for keeping track of scooter positions, battery level and other useful data coming from the physical devices. 
Provides the operation of locking and unlocking on a scooter to other contexts and it's considered the ground truth on this data.
Also responsible for defining control policies and apply them operating on the physical devices, like power save, max speed, that depend only on the scooter physical state.
Responsible for physical actuation of all the remote operations on the scooter.
#### Area of Service Context
Keeps track of service areas, scooter-area bindings and scooter position in order to detect when a scooter is crossing the area boundary and notify the other contexts when that happen.
#### Scooter Data Context
Storage of e-scooters technical and logical static information, like ID, serial number, dimensions, weight...
Is the ground truth on the identity of a scooter. 

### Rent Subdomain
*Core Subdomain*
#### Rent Context
Manages the renting operations including storing data on which scooters are rented and by whom. Provides the interface for searching and renting scooters.
Manages the logic enabling and disabling of a scooter and provides an interface to do that.
#### Trip Context
Data collection about trips done by all customers such as starting points, duration, travelled kilometers.
#### Rent Payment Context
Responsible for managing the payment policy for customer rents. Authorizes the renting operation by checking in with the payment context.

### Insight Subdomain
*Core Subdomain*
#### Drop points Planning Context
Exploits usage data from other contexts to compute drop point locations or suggestions.
Stores data about user search requests about scooters.

### Payment Subdomain
*Supporting Subdomain*
#### Payment Context
Manages all customer in-app transactions, virtual currency, and triggers real-world transactions.

### User Subdomain
*Generic Subdomain*
#### Customer Context
Keeps track of customers anagraphic data.
#### Authentication Context
Manages login data and role based authentication for all people that use the service, including admins, assistants and customers.

