# Trip

## API

### /trips

#### GET  
Returns a list of Trips.

**Query Parameters**  
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**
Code 200:
```json
{
  "pageNum": integer,
  "pageSize": integer,
  "areas": [
    {
      "id": string //EntityId
    }
  ],
  "next": string
}
```


#### POST  
Creates a new Trip.

**Body**
```json
{
  "rentId": string, //EntityId
  "scooterId": string, //EntityId
}
```

**Response**  
Code 201:
```json
{
  "id": string, //EntityId
}
```

Code 400

### /trips/{id}

**URL Parameters**  
- **id**: the EntityId that identifies the requested trip.

#### GET  
get the properties of an existing trip

**Response**  
Code 200:
```json
{
  "id": string, //EntityId
  //TODO gestire start stop come TripPoint? "start": object, //TripPoint
  "travelledDistance": number //Distance
}
```
Code 400
Code 404


## Outgoing Events

### TripStarted  
*Fires when* a new trip starts
```json
{
  "id": string //EntityId
}
```

### TripEnded  
*Fires when* a new trip ends
```json
{
  "id": string //EntityId
}
```

## Subscriptions

### Scooter Data  
- ScooterCreated
- ScooterDeleted


### Rent  
- RentRequest
- RentConfirmed
- RentStopped
