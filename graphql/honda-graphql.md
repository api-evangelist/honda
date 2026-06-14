# Honda Motor GraphQL Schema

## Overview

This document describes a conceptual GraphQL schema for Honda Motor Company's connected car and API services. Honda provides vehicle data, remote control, navigation, diagnostics, maintenance, and dealer services through its HondaLink and AcuraLink connected vehicle platforms.

## Developer Resources

- Developer Portal: https://developer.honda.com/
- GitHub: https://github.com/HondaLab
- HondaLink: https://www.honda.com/hondalink
- AcuraLink: https://www.acura.com/acuralink

## Schema File

The GraphQL schema is defined in `honda-schema.graphql` and covers the following domains:

### Vehicle Identity and Configuration
- `Vehicle`, `HondaVehicle`, `AcuraVehicle` — base and brand-specific vehicle types
- `VIN`, `Model`, `Trim`, `Year`, `Engine`, `Drivetrain`, `Transmission`, `Color` — vehicle specification types

### Vehicle Status and Telemetry
- `VehicleStatus`, `IgnitionStatus`, `DoorLockStatus`, `WindowStatus`, `HoodStatus`, `TrunkStatus` — real-time vehicle state
- `FuelLevel`, `OdoReading`, `ClimateStatus`, `HeatedSeat`, `RemoteStart` — comfort and fuel data

### Connected Services
- `HondaLink`, `AcuraLink` — connected service account and subscription types
- Remote commands: lock/unlock, climate pre-conditioning, remote start

### Safety and Driver Assistance
- `HondaSensing`, `CollisionMitigationBraking`, `LaneDepartureWarning`, `RoadDepartureWarning` — Honda Sensing suite
- `AdaptiveCruiseControl`, `BlindSpotInfo` — advanced driver assistance

### Navigation and Trips
- `GPS`, `Location`, `NavigationRoute` — positioning and routing
- `Trip`, `TripHistory`, `DriveHistory`, `FuelEfficiency` — journey and efficiency data

### Maintenance and Diagnostics
- `MaintenanceReminder`, `OilLife`, `TirePressure` — maintenance tracking
- `DiagnosticCode`, `Recall`, `Alert`, `Notification` — fault and recall management

### Dealer and Service
- `Dealer`, `DealerLocator`, `DealerInventory` — dealer search and inventory
- `ServiceHistory`, `PartsOrder`, `PartsLookup`, `ManualPDF` — service and parts

### Authentication
- `APIKey`, `OAuthToken`, `Webhook` — API access and event delivery

## Query Examples

### Get Vehicle Status

```graphql
query GetVehicleStatus($vin: String!) {
  vehicleStatus(vin: $vin) {
    vin { number }
    ignition { state lastChanged }
    doorLock { driverFront driverRear passengerFront passengerRear allLocked }
    fuelLevel { percentage distanceToEmpty unit }
    odometer { reading unit lastUpdated }
    climate { interiorTemp exteriorTemp unit isRunning }
    tirePressure { frontLeft frontRight rearLeft rearRight unit }
  }
}
```

### Remote Start Vehicle

```graphql
mutation RemoteStartVehicle($vin: String!, $input: RemoteStartInput!) {
  remoteStart(vin: $vin, input: $input) {
    success
    commandId
    estimatedReadyTime
  }
}
```

### Find Nearby Dealers

```graphql
query NearbyDealers($location: LocationInput!, $radius: Float) {
  dealerLocator(location: $location, radius: $radius) {
    dealers {
      dealerId
      name
      address
      phone
      distance
      services
      hours { day open close }
    }
  }
}
```

### Get Trip History

```graphql
query TripHistory($vin: String!, $startDate: String!, $endDate: String!) {
  tripHistory(vin: $vin, startDate: $startDate, endDate: $endDate) {
    trips {
      tripId
      startTime
      endTime
      distance
      fuelUsed
      averageSpeed
      startLocation { latitude longitude address }
      endLocation { latitude longitude address }
    }
    totalDistance
    totalFuelUsed
    averageFuelEfficiency
  }
}
```

## Authentication

Honda API services use OAuth 2.0. Obtain tokens via the Honda developer portal before making requests.

```graphql
mutation GetOAuthToken($clientId: String!, $clientSecret: String!, $scope: String) {
  authenticate(clientId: $clientId, clientSecret: $clientSecret, scope: $scope) {
    accessToken
    tokenType
    expiresIn
    scope
  }
}
```
