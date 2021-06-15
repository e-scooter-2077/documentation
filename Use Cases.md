# Use Cases
## User stories
Some user stories are taken from end users and some other are taken from the business representative, business employees.
### Story #1a
>As a customer, I want to open the mobile application and search for free scooters in my area.
### Story #1b
>As a customer, I want to be able to select a scooter near myself and make it ring in order to find it if it's hidden.
### Story #1c
>As a customer, I want to confirm on my phone and start riding. When I'm done, I want to leave the scooter where I can and confirm the end of the trip on the app.
### Story #2
>As a customer, when I'm searching a scooter, I want to know its battery level in advance.
### Story #3a
>When a scooter is taken out of the area of service borders, it locks.
### Story #3b
>As a customer, when I'm approaching the area of service border, I want to be warned about the possibility that my scooter gets locked.

_How do you want to get warned?_
>I'd like the scooter to play a specific sound, while I get a notification on my phone.
### Story #4
>As a customer, I want to be warned when the battery is running low. I want to be warned with a ringtone.
### Story #5
>When the battery goes below a threshold, the scooter enters a power save mode, during which it runs slower than normal.
### Story #6
>When the battery is running low, the scooter warns the customer, if present, that the run must end and enters a standby mode during which it's disabled.
### Story #7
>As the business manager, I want to be able to see the position and the battery level of every scooter in a map.
### Story #8
>As the business manager, I want to get suggestions about strategic places for the drop points of the next day.
### Story #9
>As a business employee I want to be able to unlock freely the scooters in order to move them.
### Story #10
>The scooter should discourage theft and vandalism by playing a message when moved while locked or disabled.
### Story #11
>As the business manager, I want the customers to pay for rides through the existing system.

_Does the legacy system keep track of all users data?_
>If I remember correctly, yes.

_From the legacy system documentation:_
The legacy payment system needs access to trip data and to customers rent/unlock requests.

## Use Case Diagram
```plantuml
@startuml
left to right direction
actor "Customer" as c
rectangle "Use Cases" {
    usecase "Rent" as rent
    usecase "Pay" as pay
    usecase "Search scooters" as search 
    usecase "Ring scooter" as ring
    usecase "Unlock scooter" as unlock
    usecase "Confirm trip end" as confirm_end
    usecase "End trip" as end_trip
    usecase "Exit Area of Service" as exit_area
    usecase "Ride the scooter" as ride
    usecase "Ride the scooter until battery runs out" as ride_lot
    confirm_end .down.> end_trip : <<includes>>
    exit_area .down.> end_trip : <<includes>>
    ride_lot .down.> ride : <<extends>>
    ride_lot .down.> end_trip : <<includes>>
    rent .down.> unlock : <<includes>>
    rent .down.> end_trip : <<includes>>
    rent .> pay : <<includes>>
}
c -- search
c -- ring
c -- rent
c -- confirm_end
c -- exit_area
c -- ride

@enduml
```