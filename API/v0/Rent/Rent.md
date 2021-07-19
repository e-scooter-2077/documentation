# Rent

## API

### /rents

#### GET
Returns a list of Rents.

**Query Parameters**  
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**
Code 200:
```json
{
  "pageNum": integer,
  "pageSize": integer,
  "rents": [
    {
      "id": string //EntityId
    }
  ],
  "next": string
}
```

### /rents/startWith/{scooterId}

**URL Parameters**  
- **scooterId**: the EntityId that identifies the requested scooter

#### POST  
Requests the start of a rent

**Body**
```json
{
  "customerId": string //EntityId
}
```

**Response**
Code 201:
```json
{
  "scooterId": string, //EntityId
  "customerId": string, //EntityId
  "rentId": string, //EntityId
}
```

Code 404
Code 400


### /rents/stop/{rentId}

**URL Parameters**  
- **rentId**: the EntityId that identifies the requested rent

#### POST  
Requests the end of a rent

**Response**
Code 201:
```json
{
  "rentId": string, //EntityId
}
```

Code 404

## Outgoing Events

### RentRequested  
*Fires when* a rent is created but still not confirmed
```json
{
  "rentId": string //EntityId
}
```

### RentConfirmed  
*Fires when* a rent is confirmed
```json
{
  "rentId": string //EntityId
}
```

### RentStopped  
*Fires when* a rent is stopped from the customer
```json
{
  "rentId": string //EntityId
}
```

### RentCanceled  
*Fires when* a rent is canceled for other reasons
```json
{
  "rentId": string //EntityId
  //TODO "reason": string //RentEndReason
}
```

## Subscriptions

### ScooterData  
- ScooterCreated
- ScooterDeleted
