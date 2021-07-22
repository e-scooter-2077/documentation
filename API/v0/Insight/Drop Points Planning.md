# Drop Points Planning

## API

### /DropPoints

#### GET  
Returns the list of the best drop points

**Query Parameters**  
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**  
Code 200:
```json
{
  "pageNum": integer,
  "pageSize": integer,
  "points": [
    {
      "id": string //EntityId
    }
  ],
  "next": string
}
```

### /DropPoints/{id}

**URL Parameters**  
- **id**: the EntityId that identifies the requested drop point.

#### GET  
get the properties of an existing drop point

**Response**  
Code 200:
```json
{
  "id": string, //EntityId
  "position": object, //GeoPoint
  "scooterNumber": number
}
```
Code 400
Code 404

## Subscriptions

### Area of Servie  
- AreaCreated
- AreaDeleted
- AreaShapeChanged
- ScooterAssignedToArea
- ScooterRemovedFromArea

### Trip  
- TripStarted
- TripEnded

### Rent  
- RentConfirmed
- RentStopped
