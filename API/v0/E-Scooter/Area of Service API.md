# Area of Service

## API

### /areas

#### GET

Returns a list of Areas of service.

**Params**  
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**
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

### /areas/{id}

#### GET

#### PUT

## Events

### AreaCreated
*Fires when* a new area is added to the System.

**Content**
```json
{
  "id": string //EntityId
}
```

### AreaRemoved
*Fires when* a new area is added to the System.
```json
{
  "id": string //EntityId
}
```

### AreaShapeChanged
*Fires when* the shape of an existing area is modified.
```json
{
  "id": string //EntityId
}
```

### ScooterAdded
*Fires when* an existing scooter is assigned to an area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

### ScooterRemoved
*Fires when* an existing scooter is removed from an area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

