# Area of Service

## API

### /areas

#### GET  
Returns a list of Areas of service.

**Query Parameters**  
- **pageNum**: number of requested page given the pageSize
- **pageSize**: elements per page

**Response**  
Code 200:  
[paginated response](../paginate.md)
```json
{
  "data": [
    {
      "id": string //EntityId
    }
  ]
}
```

#### POST
Creates a new Area of Service.

**Body**
```json
{
  "shape": object //Shape
}
```

**Response**  
Code 201:
```json
{
  "id": string, //EntityId
  "shape": object //Shape
}
```

Code 400

### /areas/{id}

**URL Parameters**  
- **id**: the EntityId that identifies the requested area.

#### GET  
get the properties of an existing area.

**Response**  
Code 200:
```json
{
  "id": string, //EntityId
  "shape": object, //Shape
  "scooters": Set[string] //Set[EntityId]
}
```
Code 400
Code 404

#### PUT  
Modify the properties of an existing area.
**Body**
```json
{
  "shape": object //Shape
}
```

**Response**  
Code 200:
```json
{
  "id": string, //EntityId
  "shape": object //Shape
}
```
Code 400
Code 404

#### DELETE
Removes an Area of Service identified by id.

**Response**  
Code 200:
```json
{
  "id": string, //EntityId
  "shape": object //Shape
}
```

Code 404

### /areas/{areaId}?contains={scooterId}

**URL Parameters**  
- **areaId**: the EntityId that identifies the area in which to search

**Query Parameters**  
- **contains**: the EntityId that identifies the requested scooter

#### GET
Returns whether a scooter belonging to an area is currently inside tha area boundary.

**Response**  
Code 200:
```json
{
  "areaId": string, //EntityId
  "scooterId": string, //EntityId
  "isInArea": boolean
}
```

Code 404: if the scooter doesn't belong to the selected area or the area doesn't exist.

### /scooters/{id}?belongsTo={areaId}

#### GET
Returns whether a scooter belongs to an Area of Service.

**URL Parameters**  
- **scooterId**: the EntityId that identifies the requested scooter

**Query Parameters**
- **belongsTo**: the EntityId that identifies the area in which to search

**Response**  
Code 200:
```json
{
  "areaId": string, //EntityId
  "scooterId": string, //EntityId
  "belongsToArea": boolean
}
```

Code 404: if the scooter or the area doesn't exist.

## Outgoing Events

<!-- TODO Not Sure -->
### AreaCreated
*Fires when* a new area is added to the System.
```json
{
  "id": string //EntityId
}
```
<!-- TODO Not Sure -->
### AreaDeleted
*Fires when* a new area is removed from the System.
```json
{
  "id": string //EntityId
}
```
<!-- TODO Not Sure -->
### AreaShapeChanged
*Fires when* the shape of an existing area is modified.
```json
{
  "id": string //EntityId
}
```

### ScooterAssignedToArea
*Fires when* an existing scooter is assigned to an area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

### ScooterRemovedFromArea
*Fires when* an existing scooter is removed from an area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

### ScooterWentOutsideArea
*Fires when* an existing scooter exits from the boundary of its area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

### ScooterReturnedInsideArea
*Fires when* an existing scooter is put back inside the boundary of its area of service.
```json
{
  "areaId": string, //EntityId
  "scooterId": string //EntityId
}
```

## Subscriptions

### Scooter Data  
- ScooterCreated
- ScooterDeleted