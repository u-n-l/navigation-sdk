# Navigation Instructions
The Navigation SDK for Flutter provides real-time navigation guidance with detailed route information, including road details, street names, speed limits, and turn directions. You receive essential data such as remaining travel time, distance to destination, and upcoming maneuvers.

The main class responsible for turn-by-turn navigation guidance is the `NavigationInstruction` class.

> 📝 **Note:** Distinguish between `NavigationInstruction` and `RouteInstruction`. `NavigationInstruction` offers real-time, turn-by-turn guidance based on your current position and is relevant only during active navigation or simulation. In contrast, `RouteInstruction` provides an overview of the entire route available immediately after calculation, with instructions that remain static throughout navigation.

## Get navigation instructions
You cannot directly instantiate navigation instructions. The SDK provides them during active navigation. For detailed guidance, see the [Getting Started with Navigation Guide](../08-Navigation/02-Get%20Started%20with%20Navigation.md#get-started-with-navigation).

There are two ways to get navigation instructions:

- **Via callback** - NavigationInstruction instances are provided through callbacks in the `startNavigation` and `startSimulation` methods
- **Via service** - The `NavigationService` class provides a `getNavigationInstruction` method that returns the current navigation instruction
  
> ⚠️ **Attention:** Ensure navigation or simulation is active before calling `getNavigationInstruction`.

## Understand the structure
The `NavigationInstruction` class contains the following members:

| Member | Type | Description |
|--------|------|-------------|
| `currentCountryCodeISO` | `String` | Returns the ISO 3166-1 alpha-3 country code for the current navigation instruction. An empty string indicates no country. |
| `currentStreetName` | `String` | Returns the current street name. |
| `currentStreetSpeedLimit` | `double` | Returns the maximum speed limit on the current street in meters per second. Returns `0` if unavailable. |
| `driveSide` | `DriveSide` | Returns the drive side flag of the currently traveled road. |
| `hasNextNextTurnInfo` | `bool` | Returns `true` if next-next turn information is available. |
| `hasNextTurnInfo` | `bool` | Returns `true` if next turn information is available. |
| `instructionIndex` | `int` | Returns the index of the current route instruction within the current route segment. |
| `laneImg` | `LaneImg` | Returns a customizable image of the current lane configuration. The user is responsible for validating the image. |
| `navigationStatus` | `NavigationStatus` | Returns the current navigation or simulation status. |
| `nextCountryCodeISO` | `String` | Returns the ISO 3166-1 alpha-3 country code for the next navigation instruction. |
| `nextNextStreetName` | `String` | Returns the next-next street name. |
| `nextNextTurnDetails` | `TurnDetails` | Returns full details for the next-next turn. Used for customizing turn display in the UI. |
| `nextNextTurnImg` | `Img` | Returns a simplified schematic image of the next-next turn. The user is responsible for validating the image. |
| `nextNextTurnInstruction` | `String` | Returns the textual description for the next-next turn. |
| `getNextSpeedLimitVariation` | `NextSpeedLimit` | Returns the next speed limit variation within the specified check distance. |
| `nextStreetName` | `String` | Returns the next street name. |
| `nextTurnDetails` | `TurnDetails` | Returns full details for the next turn. Used for customizing turn display in the UI. |
| `nextTurnImg` | `Img` | Returns a simplified schematic image of the next turn. The user is responsible for validating the image. |
| `nextTurnInstruction` | `String` | Returns the textual description for the next turn. |
| `remainingTravelTimeDistance` | `TimeDistance` | Returns the remaining travel time in seconds and distance in meters. |
| `remainingTravelTimeDistanceToNextWaypoint` | `TimeDistance` | Returns the remaining travel time in seconds and distance in meters to the next waypoint. |
| `currentRoadInformation` | `List<RoadInfo>` | Returns the current road information list. |
| `nextRoadInformation` | `List<RoadInfo>` | Returns the next road information list. |
| `nextNextRoadInformation` | `List<RoadInfo>` | Returns the next-next road information list. |
| `segmentIndex` | `int` | Returns the index of the current route segment. |
| `signpostDetails` | `SignpostDetails` | Returns extended signpost details. |
| `signpostInstruction` | `String` | Returns the textual description for signpost information. |
| `timeDistanceToNextNextTurn` | `TimeDistance` | Returns the time (seconds) and distance (meters) to the next-next turn. If unavailable, values for the next turn are returned. |
| `timeDistanceToNextTurn` | `TimeDistance` | Returns the time (seconds) and distance (meters) to the next turn. |
| `traveledTimeDistance` | `TimeDistance` | Returns the traveled time in seconds and distance in meters. |


The `nextTurnInstruction` field provides text suitable for UI display. Use the `onTextToSpeechInstruction` callback for text-to-speech output.

## Access turn details
### Get next turn details
Extract detailed instructions for the next turn along the route. Use this information in your navigation UI to display upcoming maneuvers with images or detailed text:

```
// If hasNextTurnInfo is false some details are not available
bool hasNextTurnInfo = navigationInstruction.hasNextTurnInfo;

if (hasNextTurnInfo) {
    // The next turn instruction
    String nextTurnInstruction = navigationInstruction.nextTurnInstruction;

    // The next turn details
    TurnDetails turnDetails = navigationInstruction.nextTurnDetails;

    // Turn event type (continue straight, turn right, turn left, etc.)
    TurnEvent event = turnDetails.event;

    // The image representation of the abstract geometry
    Uint8List? abstractGeometryImage = turnDetails.getAbstractGeometryImage(
        size: Size(300, 300),
    );

    // The image representation of the next turn
    Uint8List? turnImage = navigationInstruction.getNextTurnImage(size: Size(300, 300));

    // Roundabout exit number (-1 if not a roundabout)
    int roundaboutExitNumber = turnDetails.roundaboutExitNumber;
}
```
See the [TurnDetails](08-Routes/#turn-details.md) guide for more details.

### Get next-next turn details
Access details about the turn following the next one to provide a preview of upcoming maneuvers:
```
// If hasNextNextTurnInfo is false some details are not available
bool hasNextNextTurnInfo = navigationInstruction.hasNextNextTurnInfo;

if (hasNextNextTurnInfo) {
    String nextNextTurnInstruction = navigationInstruction.nextNextTurnInstruction;

    TurnDetails nextNextTurnDetails = navigationInstruction.nextNextTurnDetails;

    Uint8List? nextNextTurnImage = navigationInstruction.getNextNextTurnImage(size: Size(300, 300));
}
```
> 📝 **Note:** The `hasNextNextTurnInfo` returns false if the next instruction is the destination.

You can apply the same operations from next turn details to next-next turn details.

## Get street information
### Access current street details
Retrieve information about the current road:

```
// Current street name
String currentStreetName = navigationInstruction.currentStreetName;

// Road info related to the current road
List<RoadInfo> currentRoadInfo = navigationInstruction.currentRoadInformation;

// Country ISO code
String countryCode = navigationInstruction.currentCountryCodeISO;

// The drive direction (left or right)
DriveSide driveDirection = navigationInstruction.driveSide;
```
> 📝 **Note:** Some streets may not have an assigned name. In such cases, `currentStreetName` returns an empty string.

The `RoadInfo` class provides additional details, including the road name and shield type, which correspond to official road codes.

For example, `currentStreetName` might return "Bloomsbury Street," while the `roadname` field in the associated `RoadInfo` instance provides the official designation, such as "A400."

### Access next & next-next street details
Retrieve information about the next street and the street following it:
```
// Street name
String nextStreetName = navigationInstruction.nextStreetName;
String nextNextStreetName = navigationInstruction.nextNextStreetName;

// Road info
List<RoadInfo> nextRoadInformation = navigationInstruction.nextRoadInformation;
List<RoadInfo> nextNextRoadInformation = navigationInstruction.nextNextRoadInformation;

// Next country iso code
String nextCountryCodeISO = navigationInstruction.nextCountryCodeISO;
```
These fields have the same meanings as the current street fields.

>  🚨 **Alert**: Ensure `hasNextTurnInfo` and `hasNextNextTurnInfo` are true before accessing the respective fields. This prevents errors and ensures data availability.

## Get speed limit information
The `NavigationInstruction` class provides information about the current road's speed limit and upcoming speed limits within a specified distance.

Retrieve speed limit details and handle various scenarios:
```
// The current street speed limit in m/s (0 if not available)
double currentStreetSpeedLimit = navigationInstruction.currentStreetSpeedLimit;

// Get next speed limit in 500 meters
NextSpeedLimit nextSpeedLimit = navigationInstruction.getNextSpeedLimitVariation(checkDistance: 500);
// Coordinates where next speed limit changes
Coordinates coordinatesWhereSpeedLimitChanges = nextSpeedLimit.coords;
// Distance to where the next speed limit changes
int distanceToNextSpeedLimitChange = nextSpeedLimit.distance;
// Value of the next speed limit (m/s)
double nextSpeedLimitValue = nextSpeedLimit.speed;

if (distanceToNextSpeedLimitChange == 0 && nextSpeedLimitValue == 0) {
    showSnackbar("The speed limit does not change within the specified interval");
} else if (nextSpeedLimitValue == 0) {
    showSnackbar(
        "The speed limit changes in the specified interval but the value is not available");
} else {
    showSnackbar(
        "The next speed limit changes to $nextSpeedLimitValue m/s in $distanceToNextSpeedLimitChange meters");
}
```

## Display Lane Guidance
Use the lane image to illustrate the correct lane for upcoming turns:

```
final LaneImg laneImage = navigationInstruction.laneImage;
final Uint8List? laneImageData = laneImage.getRenderableImageBytes(size: Size(500, 300), format: ImageFileFormat.png);
```
![Lane image containing three lanes
](image-3.png)

## Change instruction language
Navigation instruction texts follow the language set in the SDK. See the [Internationalization Guide](/02-Get%20Started/04-Internationalization.md) for more details.

