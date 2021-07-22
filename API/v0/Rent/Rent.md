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

### /rents/{rentId}

**URL Parameters**  
- **rentId**: the EntityId that identifies the requested rent

#### GET  
Requests the end of a rent

**Response**
Code 201:
```json
{
  "scooterId": string, //EntityId
  "customerId": string, //EntityId
  "rentId": string, //EntityId
  "confirmed": boolean,
  "end": boolean
}
```

Code 404

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
  "rentId": string, //EntityId
}
```

### RentConfirmed  
*Fires when* a rent is confirmed
```json
{
  /* TODO ci vanno? 
  "scooterId": string, //EntityId
  "customerId": string, //EntityId
  */
  "rentId": string, //EntityId
  "confirm": number //Timestamp
}
```

### RentStopped  
*Fires when* a rent is stopped for the reason specified in the body
```json
{
  "rentId": string, //EntityId
  "reason": string, //RentEndReason
  "stop": number //Timestamp
}
```

## Subscriptions

### ScooterData  
- ScooterCreated
- ScooterDeleted

### Rent Payment
- RentAuthorized 
- RentNotAuthorized
- CreditExhaustedForRent
