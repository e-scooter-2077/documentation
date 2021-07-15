# ScooterData

## API

### /scooters

#### GET  
Returns a list of Scooters.

**Query Params**   
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**  
Code 200:
```json
{
  "pageNum": integer,
  "pageSize": integer,
  "scooter": [
    {
      "id": string //EntityId
    }
  ],
  "next": string
}
```

#### POST  
Creates a new Scooter.

**Body**  
```json
{
    "id": string, //EntityId
    "serialNumber": string, //SerialNumber
    "weight": integer, //Weight
    "model": string, //ModelInfo
}
```

**Response**  
Code 201:
```json
{
  "id": string //EntityId
}
```

Code 400

### /scooters/{id}

**URL Parameters**  
- **id**: the EntityId that identifies the requested scooter.

#### GET  
Returns the detail of the specified scooter.

**Response**
Code 200:
```json
{
  "id": string, //EntityId
  "serialNumber": string, // SerialNumber
  "weight": integer, //Weight
  "model": string, //ModelInfo
}
```

Code 404


#### DELETE  
Removes the specified scooter.  

**Response**
Code 200:
```json
{
  "id": string //EntityId
}
```

Code 404

## Outgoing Events

### ScooterCreated
*Fires when* a new scooter is added to the System.
```json
{
  "id": string //EntityId
}
```

### ScooterDeleted
*Fires when* a new scooter is removed from the System.
```json
{
  "id": string //EntityId
}
```