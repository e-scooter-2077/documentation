# Rent Payment

## Outgoing Events

### RentAuthorized  
*Fires when* a rent is authorized
```json
{
  "rentId": string //EntityId
}
```

### RentNotAuthorized  
*Fires when* a rent is authorized
```json
{
  "rentId": string //EntityId
}
```

### CreditExhaustedForRent  
*Fires when* the credit isn't enough to continue the rent
```json
{
  "rentId": string //EntityId
}
```

## Subscriptions

### Rent  
- RentRequested
- RentConfirmed
- RentStopped
