# Traffic Events
The Navigation SDK for Flutter provides real-time information about traffic events such as delays, roadworks, and accidents.

When enabled and supported by your map style, traffic events appear as red overlays on affected road segments.

**Event sources:**
-  **UNL** - Provide up-to-date traffic data when online
-  **GrabMaps Traffic Data** -  Provide up-to-date traffic data for Southeast Asia when online
-  **User-defined roadblocks** - Blacklist specific road segments or areas

**Impact zones:**

- **Path-based** - Follows the shape of a road
- **Area-based** - Covers a larger geographic area  

The central class for handling traffic events and roadblocks is `TrafficEvent`. You can obtain instances through user interaction with the map or as part of roadblock operations. For route-specific traffic data, use the `RouteTrafficEvent` class, which extends `TrafficEvent` with detailed route information.

Traffic events, including delays and user-defined roadblocks, are fully integrated into the routing and navigation logic. This ensures that calculated routes dynamically account for traffic conditions and any restricted segments.

## TrafficEvent structure
The `TrafficEvent` class contains the following members:

| Member | Type | Description |
|--------|------|-------------|
| `isRoadblock` | `bool` | Returns `true` if the event represents a roadblock. |
| `delay` | `int` | Estimated delay in seconds. Returns `-1` if unknown. |
| `length` | `int` | Length in meters of the affected road segment. Returns `-1` if unknown, such as for area-based roadblocks. |
| `impactZone` | `TrafficEventImpactZone` | Indicates whether the event affects a point or an area. |
| `referencePoint` | `Coordinates` | Central coordinate of the event. Returns `(0, 0)` for area-based events. |
| `boundingBox` | `RectangleGeographicArea` | Geographical bounding box surrounding the event. |
| `area` | `GeographicArea` | Geographic area associated with the event (see `TrafficService.addPersistentRoadblockByArea`). If no area is provided, this is the same as `boundingBox`. |
| `isAntiArea` | `bool` | Indicates whether the impact zone is the anti-area of the event area. Valid only for area impact zones. |
| `isActive` | `bool` | Indicates whether the traffic event is active (i.e. has started). |
| `isExpired` | `bool` | Indicates whether the traffic event is expired (i.e. has ended). |
| `description` | `String` | Human-readable description of the traffic event. For user-defined roadblocks, this includes the ID. |
| `eventClass` | `TrafficEventClass` | Classification of the traffic event. |
| `eventSeverity` | `TrafficEventSeverity` | Severity level of the traffic event. |
| `getImage({size, format})` | `Uint8List?` | Returns an image representing the traffic event. |
| `img` | `Img` | Returns the traffic event image in internal format. |
| `previewUrl` | `String` | Returns a URL to preview the traffic event. Returns an empty string if unavailable (e.g. for user-defined roadblocks). |
| `isUserRoadblock` | `bool` | Returns `true` if the event is a user-defined roadblock. |
| `affectedTransportModes` | `Set<TrafficTransportMode>` | Returns all transport modes affected by the event. |
| `startTime` | `DateTime?` | UTC start time of the traffic event, if available. |
| `endTime` | `DateTime?` | UTC end time of the traffic event, if available. |
| `hasOppositeSibling` | `bool` | Returns `true` if a sibling event exists in the opposite direction. Relevant for path-based events. |


## Understand RouteTrafficEvent structure
The `RouteTrafficEvent` class extends `TrafficEvent` with route-specific information:

| Member | Type | Description |
|--------|------|-------------|
| `distanceToDestination` | `int` | Returns the distance in meters from the event’s position on the route to the destination. Returns `0` if unavailable. |
| `from` | `Coordinates` | Starting point of the traffic event on the route. |
| `to` | `Coordinates` | End point of the traffic event on the route. |
| `fromLandmark` | `(Landmark, bool)` | Returns the starting point as a landmark and a flag indicating whether the data is cached locally. |
| `toLandmark` | `(Landmark, bool)` | Returns the end point as a landmark and a flag indicating whether the data is cached locally. |
| `asyncUpdateToFromData()` | `void Function(GemError)` | Asynchronously updates the address and description information of the `from` and `to` landmarks from the server. |
| `cancelUpdate()` | `void` | Cancels any pending asynchronous update request for landmark data. |

## Use traffic events
Traffic events provide insights into road conditions, delays, and closures:

- **Route traffic information** - See the [Get ETA and Traffic information guide](../07-Routing/02-Get%20Started%20wtih%20Routing.md#retrieve-time-and-distance-information)
- **User-defined roadblocks** - See the [Roadblocks guide](../08-Navigation/05-Roadblocks.md#remove-roadblocks)

### Event classifications
The `TrafficEventClass` provides the following event types:

- `trafficRestrictions`
- `roadworks`
- `parking`
- `delays`
- `accidents`
- `roadConditions`

### Severity levels
The `TrafficEventSeverity` enum includes:

- `stationary`
- `queuing`
- `slowTraffic`
- `possibleDelay`
- `unknown`