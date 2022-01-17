# Ubiquitous Language

## E-Scooter service
A service that allows [customers](#customer) to [rent](#rent) [electric scooters](#electric-scooter) placed in public areas for a [trip](#trip) within the boundaries of an [area of service](#area-of-service).

## Electric Scooter
Aliases: e-scooter, scooter  
Motorized vehicle with two wheels, usable by a person standing on it. It's equipped with some sensors and other devices.

### E-Scooter equipment
#### Electric engine
Uses power from the [battery](#battery) to power the front wheel spin.
#### Wheels and tires
The scooter runs on two wheels with air-inflated tires; the front wheel containes a rotating [electric engine](#electric-engine).
#### Battery
Scooter power supply; rechargeable; electric; has finite capacity, power...
#### GPS sensor
Global Positioning System; estimates a position on Earth by negotiating it with satellites; based on a free service; works better in open places
#### Speedometer
Measures speed using wheel rotations.
#### Accelerometer
Measures acceleration forces along the three axis
#### Electronic wheel block
Aliases: electric break, electric block  
If turned on, the wheel becomes very hard to rotate
#### Headlight
Light source embedded in the front to enable night usage
#### Manual Breaks
Aliases: Breaks, default breaks, disk breaks, rear break  
Friction based stopping system;
Placed on the back wheel;
Controlled by a hand lever.
#### Accelerator
Handle that controls the speed; If the handle isn't turned the [electric break](#electronic-wheel-block) is activated.
#### Speaker
A device that can play sounds and can be remotely activated.

### Mode
Aliases: State  
One of the possible states in which the scooter can be. In each mode, the scooter has a different behavior.
#### Active mode
A mode in which the scooter has no electric limit on power.
#### Power Saving mode
A mode in which the scooter uses a lower maximum speed to limit power consuption. This happens after the battery gets below a [threshold](#power-saving-threshold)
##### Power Saving threshold
The battery level below which the scooter enters Power Saving mode.
#### Standby mode
A mode in which the scooter must be [disabled](#disable) due to extremely low battery.
##### Standby threshold
The battery level below which the scooter enters Standby mode.

### _Operations_
#### Unlock
To turn off the [electric block](#electronic-wheel-block)
#### Lock
To turn on the [electric block](#electronic-wheel-block).
#### Ride
Use the scooter to move.
#### Disable
To deny the possibility that a [customer](#customer) [rents](#rent) a scooter.
#### Enable
To allow the possibility that a [customer](#customer) [rents](#rent) a scooter if the business policies allow it.
#### Maintain
Repair any damage and/or recharge the battery.
#### Rent
As a [customer](#customer), paying or exploiting a [subscription](#subscription) for [unlocking](#unlock) an [e-scooter](#electric-scooter). This action lasts until the scooter is [locked](#lock) again (in normal conditions).
#### Assign to area
An e-scooter can be assigned to an area if, from that moment, that e-scooter mutually exclusively [belongs](#belong-to-area) to that [area](#area-of-service).
#### Recover
_See [recovery](#recovery)._
#### Ring
Make the speaker play a ringtone, useful to find it.

### _Relationships_
#### Belong to area
The [e-scooter](#electric-scooter) can be only rented and ridden inside that [area](#area-of-service).

## Customer
Alias: user.
A customer for the [e-scooter service](#e-scooter-service).
### Anagraphic information
Collection of data about the customer, including first name, date of birth, and all the other possible information useful for the [e-scooter service](#e-scooter-service).
### Subscription
A customer has it if they are able to [rent](#rent) [e-scooters](#electric-scooter) without paying upfront.

## Admin
Alias: manager
Works for the [E-Scooter Service](#e-scooter-service). Monitors and manages [scooters](#e-scooter) from a control dashboard.

## Recovery
The act of picking up an [e-scooter](#electric-scooter), providing [maintenance](#maintenance) and dropping it somewhere inside the [area of service](#area-of-service) sometime later. The scooter may be unlocked if necessary.
### Recovery assistant
Aliases: assistant
Works for the [e-scooter service](#e-scooter-service) and does recoveries with a [recovery van](#recovery-van).
### Recovery van
A vehicle that allows [assistants](#recovery-assistant) to recover [e-scooters](#electric-scooter).

## Map
Bidimensional, polar representation of the Earth surface. Shows, for example, streets, buildings, water courses; what scooters can or cannot cross.
### Area of service
Aliases: Service Area
The map portion inside which [e-scooters](#electric-scooter) [assigned](#assign) to it can be rented.
#### Drop Point
A place of interest, inside an area of service, where [electric scooter](#electric-scooter) are usually dropped after [recovery](#recovery).

## Trip
Information about a [rent](#rent) period, during and after its lasting.
### _Properties_
#### Rider
The rider is the [customer](#customer) that [rents](#rent) the [e-scooter](#electric-scooter) during the trip.
#### Vehicle
The [scooter](#electric-scooter) used for transportation.
#### Distance
The distance, in meters, traveled during the trip.
#### Start position
Aliases: start  
A point inside the [area of service](#area-of-service) where the vehicle was [unlocked](#unlock).
#### End position
Aliases: end  
A point inside the [map](#map) where the vehicle was [locked](#lock) because the [rent](#rent) ended.

## Rent process
*See scooter operation [rent](#rent).*
### Authorizing Rent
A rent is authorized when the [customer](#customer) has enough [virtual currency](#virtual-currency) to pay the [unlocking fee](#unlocking-fee).
### Rejecting Rent
A rent is rejected when the [customer](#customer) has not enough [virtual currency](#virtual-currency) to pay the [unlocking fee](#unlocking-fee).
### Confirming Rent
A rent is confirmed when it's authorized. This marks the starting time of the rent for [time-based charging](#time-based-charging).

## Payment
A payment can be either:
- a [real-world payment](#real-world-payment)
- an [in-app payment](#in-app-payment)
### Payment legacy system
The preexisting service that manages customer data and payments for the [e-scooter service](#e-scooter-service).
### Virtual Currency
The amount of imaginary money for every customer. Used to pay scooter [rents](#rent).
It can be obtained through [real-world purchases](#real-world-payment).
### Unlocking fee
The price to unlock a scooter before initiating a rent. Charged only once per rent.
### Time based charging
The process of charging an amount of virtual currency depending on how much time a rent is taking.
### In-App Payment
Aliases: in-app purchase  
It's a [virtual currency](#virtual-currency) transaction inside the system.
### Real-world payment
Aliases: real-world purchase  
It's a money transaction from the [customer](#customer) to the business bank account.
