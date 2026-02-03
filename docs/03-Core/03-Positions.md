# Positions
This page covers position data representation using `GemPosition` and `GemImprovedPosition` classes for GPS-based systems.

> 💡 **Tip:**  Don't confuse `Coordinates` with `Position` classes. The `Coordinates` class represents geographic locations (latitude, longitude, altitude) and is widely used throughout the SDK. In contrast, `GemPosition` and `GemImprovedPosition` classes contain additional sensor data and primarily represent the user's location and movement details.

## Raw position data
Raw position data represents unprocessed GPS sensor data from devices. It corresponds to the `GemPosition` interface.

## Map matched position data
Map matching aligns raw GPS data with a digital map, correcting inaccuracies by snapping the position to the nearest logical location (such as roads). It corresponds to the `GemImprovedPosition` interface.

## Compare position types
Map matched positions provide more information than raw positions:

| Attribute | Raw | Map Matched | When Available | Description |
|----------|-----|-------------|----------------|-------------|
| `acquisitionTime` | ✅ | ✅ | always | System time when the data was collected from sensors. |
| `satelliteTime` | ✅ | ✅ | always | Satellite timestamp when the position was collected by sensors. |
| `provider` | ✅ | ✅ | always | Provider type (GPS, network, unknown). |
| `latitude & longitude` | ✅ | ✅ | hasCoordinates | Latitude and longitude of the position in degrees. |
| `altitude` | ✅ | ✅ | hasAltitude | Altitude at the given position. May be negative. |
| `speed` | ✅ | ✅ | hasSpeed | Current speed (always non-negative). |
| `speedAccuracy` | ✅ | ✅ | hasSpeedAccuracy | Speed accuracy (always non-negative). Typical accuracy is ~2 m/s in good conditions. |
| `course` | ✅ | ✅ | hasCourse | Direction of movement in degrees (0° north, 90° east, 180° south, 270° west). |
| `courseAccuracy` | ✅ | ✅ | hasCourseAccuracy | Heading accuracy in degrees (typical accuracy ~25°). |
| `accuracyH` | ✅ | ✅ | hasHorizontalAccuracy | Horizontal accuracy in meters (always positive). Typical range is 5–20 meters. |
| `accuracyV` | ✅ | ✅ | hasVerticalAccuracy | Vertical accuracy in meters (always positive). |
| `fixQuality` | ✅ | ✅ | always | Accuracy quality: `inertial`, `low`, `high`, or `invalid`. |
| `coordinates` | ✅ | ✅ | hasCoordinates | Coordinates of the position. |
| `roadModifiers` | ❌ | ✅ | hasRoadLocalization | Road modifiers such as tunnel, bridge, ramp, etc. |
| `speedLimit` | ❌ | ✅ | always | Speed limit of the current road in m/s. Returns `0` if unavailable. |
| `terrainAltitude` | ❌ | ✅ | hasTerrainData | Terrain altitude in meters. May be negative and differ from `altitude`. |
| `terrainSlope` | ❌ | ✅ | hasTerrainData | Terrain slope in degrees (positive for ascent, negative for descent). |
| `address` | ❌ | ✅ | always | Current address information. |

> 📝 **Note:** The `speedLimit` field may not always have a value, even if the position is map matched. This can happen if data is unavailable for the current road segment or if the position is not on a road. In such cases, the `speedLimit` field will be set to 0.

> 💡 **Tip:** To check if a user is exceeding the legal speed limit, use the `AlarmService` class. Refer to the speed warnings guide for more details.

