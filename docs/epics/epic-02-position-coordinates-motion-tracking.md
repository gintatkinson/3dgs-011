---
issue_id: 8
title: "Geographic Location: Position Coordinates and Motion Tracking"
type: "epic"
generation_mode: "subagent"
spec_source: "RFC 9179: A YANG Grouping for Geographic Locations"
---

# Epic: Geographic Location: Position Coordinates and Motion Tracking

## 1. Context
Provide geographic position coordinates and motion tracking for objects located on or relative to an astronomical body. This Epic covers two coordinate representations (ellipsoidal latitude/longitude/height and Cartesian X/Y/Z), a three-dimensional velocity vector for moving objects, and temporal metadata (recording timestamp and validity expiration). Together these elements describe where an object is, how it is moving, and when the data was recorded or expires.

## 2. Requirements & Checklist
- [ ] [#3](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-03-ellipsoid-coordinate-positioning.md) - Specify Ellipsoid Geodetic Coordinates (semantic linkage: provides latitude/longitude/height ellipsoidal positioning)
- [ ] [#4](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-04-cartesian-coordinate-positioning.md) - Specify Cartesian Spatial Coordinates (semantic linkage: provides X/Y/Z Cartesian positioning alternative)
- [ ] [#5](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-05-velocity-vector-tracking.md) - Track Velocity Vector for Moving Objects (semantic linkage: captures v-north/v-east/v-up velocity components with speed and heading derivation)
- [ ] [#6](https://github.com/gintatkinson/3dgs-011/blob/main/docs/features/feat-06-temporal-location-lifecycle.md) - Manage Temporal Location Lifecycle and Expiration (semantic linkage: provides timestamp and valid-until temporal metadata)

### Associated Use Cases & User Stories

#### Associated Use Cases
*(to be populated during Phase 3)*

#### Associated User Stories
*(to be populated during Phase 2)*

## 3. Architecture and System Interaction Diagrams

### Subsystem Component Definition
```mermaid
classDiagram
    class PositionMotionSubsystem {
        <<component>>
        +Real[0..1] getLatitude()
        +Real[0..1] getLongitude()
        +Real[0..1] getHeight()
        +Real[0..1] getX()
        +Real[0..1] getY()
        +Real[0..1] getZ()
        +Real[0..1] getVNorth()
        +Real[0..1] getVEast()
        +Real[0..1] getVUp()
        +String[0..1] getTimestamp()
        +String[0..1] getValidUntil()
        +Boolean[1] setEllipsoidLocation(Real lat Real lon Real height)
        +Boolean[1] setCartesianLocation(Real x Real y Real z)
        +Boolean[1] setVelocity(Real vn Real ve Real vu)
        +Real[1] deriveSpeed()
        +Real[1] deriveHeading()
        +Boolean[1] isExpired()
    }
    class GeoLocationSystem {
        <<component>>
    }
    GeoLocationSystem --> PositionMotionSubsystem : configures
```

### System-Level UML Class Diagram
```mermaid
classDiagram
    class PositionMotionSubsystem {
        <<component>>
    }
    class Location {
        <<choice>>
    }
    class EllipsoidLocation {
        +Real latitude [0..1]
        +Real longitude [0..1]
        +Real height [0..1]
    }
    class CartesianLocation {
        +Real x [0..1]
        +Real y [0..1]
        +Real z [0..1]
    }
    class VelocityVector {
        +Real vNorth [0..1]
        +Real vEast [0..1]
        +Real vUp [0..1]
    }
    class TemporalMetadata {
        +String timestamp [0..1]
        +String validUntil [0..1]
    }
    PositionMotionSubsystem *-- Location
    Location <|-- EllipsoidLocation
    Location <|-- CartesianLocation
    PositionMotionSubsystem *-- VelocityVector
    PositionMotionSubsystem *-- TemporalMetadata
```

## 4. Operational Considerations
When locations are nested (e.g., a building containing routers), the reference-frame may be inherited from the containing object. For objects in non-simple motion (frequently changing), the data model can either add additional motion data or require more frequent location queries. The velocity vector is designed for relatively stable motion and can track slow movement like continental drift. Valid-until enables stale data detection for temporal data lifecycle management.

## 5. Security & Governance
Location data may be sensitive (customer device locations, infrastructure positions). Read access SHOULD be controlled. The timestamp and valid-until fields could be used to audit when location data was recorded. Expired location data (past valid-until) SHOULD be clearly indicated to consumers. Writable data nodes are governed by NETCONF/RESTCONF access control models.

## System State Machine Diagram
```mermaid
stateDiagram-v2
    [*] --> NoLocation
    NoLocation --> EllipsoidSet : setEllipsoid [validCoordinates == true] / storeLatLonHeight
    NoLocation --> CartesianSet : setCartesian [validCoordinates == true] / storeXYZ
    EllipsoidSet --> CartesianSet : setCartesian / clearEllipsoid
    CartesianSet --> EllipsoidSet : setEllipsoid / clearCartesian
    EllipsoidSet --> MotionTracked : setVelocity / storeVelocityVector
    CartesianSet --> MotionTracked : setVelocity / storeVelocityVector
    MotionTracked --> Expired : timeExpired [currentTime > validUntil] / markStale
    MotionTracked --> EllipsoidSet : clearVelocity / removeVelocity
    Expired --> NoLocation : resetLocation / clearAll
    Expired --> EllipsoidSet : refreshLocation [validUntil > currentTime] / restoreValidity
```

### Specification Context
Location is specified using two or three coordinate values: latitude/longitude/height (ellipsoid) or X/Y/Z (Cartesian), with their exact meanings defined by the geodetic-datum. For objects in relatively stable motion, a three-dimensional velocity vector (v-north, v-east, v-up in m/s) is provided. The timestamp records when location was measured; valid-until defines expiration.

## 6. Source References
Structural Schema: ietf-geo-location@2022-02-11.yang — `location` choice, `velocity` container, `timestamp` leaf, `valid-until` leaf
Normative Specification: RFC 9179 Sections 2.2, 2.3, 2.4, 2.5
