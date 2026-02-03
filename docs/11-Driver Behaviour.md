# Driver Behaviour
The Driver Behaviour feature analyzes and scores driver behavior during trips, identifying risky patterns and providing safety scores. It tracks real-time and session-level driving events such as harsh braking, cornering, or ignoring traffic signs.

Use this data to provide user feedback, identify unsafe habits, and assess safety levels over time. All information is processed using on-device sensor data via the configured `DataSource` and optionally matched to the road network when `useMapMatch` is enabled.

## Start and Stop Analysis
Start a session using the `startAnalysis` method of the `DriverBehaviour` object. Stop the session using `stopAnalysis`, which returns a `DriverBehaviourAnalysis` instance.

```
final driverBehaviour = DriverBehaviour(
  dataSource: myDataSource,
  useMapMatch: true,
);

bool started = driverBehaviour.startAnalysis();

// ... after some driving

DriverBehaviourAnalysis? result = driverBehaviour.stopAnalysis();

if (result == null){
  print("The analysis is invalid and cannot be used");
  return;
}
```
>  🚨 **Alert**: All `DriverBehaviourAnalysis` instances expose an `isValid` getter. Verify this property before accessing the data.

## Inspect Driving Session Summary
The result returned by `stopAnalysis()` or `lastAnalysis` getter contains aggregate and detailed trip information:

```
if (result == null) {
  print("The analysis is invalid and cannot be used");
  return;
}

int startTime = result.startTime;
int finishTime = result.finishTime;
double distance = result.kilometersDriven;
double drivingDuration = result.minutesDriven;
double speedingTime = result.minutesSpeeding;
````
The session also includes risk scores:

```
DrivingScores? scores = result.drivingScores;
if (scores == null) {
  showSnackbar("No driving scores available");
  return;
}

double speedRisk = scores.speedAverageRiskScore;
double brakingRisk = scores.harshBrakingScore;
double fatigue = scores.fatigueScore;
double overallScore = scores.aggregateScore;
```
> 📝 **Note:**  Each score ranges from 0 (unsafe) to 100 (safe). A score of -1 indicates invalid or unavailable data.

## Inspect Driving Events
Use the `drivingEvents` property to access detected driving incidents:
```
List<MappedDrivingEvent> events = result.drivingEvents;
for (final event in events) {
  print("Event at ${event.latitudeDeg}, ${event.longitudeDeg} at ${event.time} with type ${event.eventType}");
}
```
## Driving Event Types
Event types are defined by the `DrivingEvent` enum:

| Enum Value | Description |
|------------|-------------|
| `noEvent` | No event |
| `startingTrip` | Starting a trip |
| `finishingTrip` | Finishing a trip |
| `resting` | Resting |
| `harshAcceleration` | Harsh acceleration |
| `harshBraking` | Harsh braking |
| `cornering` | Cornering |
| `swerving` | Swerving |
| `tailgating` | Tailgating |
| `ignoringSigns` | Ignoring traffic signs |

## Get Real-time Feedback
Fetch real-time scores during ongoing analysis:
```
DrivingScores? instantScores = driverBehaviour.instantaneousScores;
```
These scores reflect current driver behavior and provide immediate in-app feedback.

## Stop Analysis and Get Last Analysis
Stop an ongoing analysis:

```
DriverBehaviourAnalysis? analysis = driverBehaviour.stopAnalysis();
if (analysis == null) {
  print("No valid analysis available");
  return;
}
```
Retrieve the last completed analysis:

```
DriverBehaviourAnalysis? lastAnalysis = driverBehaviour.getLastAnalysis();
if (lastAnalysis == null) {
  print("No valid analysis available");
  return;
}
```
## Retrieve Past Analyses
Access all completed sessions stored locally:
```
List<DriverBehaviourAnalysis> pastSessions = driverBehaviour.allDriverBehaviourAnalyses;
```
Obtain a combined analysis over a time interval:

```
DateTime start = DateTime.now().subtract(Duration(days: 7));
DateTime end = DateTime.now();

DriverBehaviourAnalysis? combined = driverBehaviour.getCombinedAnalysis(start, end);
```

## Analyses Storage Location
Analyses are stored locally on the device in a `DriverBehaviour` folder inside the app's directory (at the same level as Data).

## Clean Up Data
Erase older sessions to save space or comply with privacy policies:

```
driverBehaviour.eraseAnalysesOlderThan(DateTime.now().subtract(Duration(days: 30)));
```
> 📝 **Note:** Driver behaviour analysis requires a properly configured `DataSource`. See the [Positioning guide](../05-Positioning%20&%20Sensors/03-Get%20Started%20wtih%20Positioning.md) to set up your data pipeline. Start and stop the analysis appropriately and avoid frequent interruptions or overlapping sessions.

## Enable Background Location
To use driver behaviour features while the app is in the background, configure both iOS and Android platforms.

Refer to the [Background Location](./05-Positioning%20&%20Sensors/08-Background%20Location.md) guide for detailed configuration instructions.
