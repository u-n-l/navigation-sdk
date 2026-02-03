# Areas Alarms
Trigger operations when users enter or exit defined geographic areas using the built-in `AlarmService` class.

## Add Areas to Monitor
Define geographic areas and invoke the `monitorArea` method on your `AlarmService` instance. You can monitor three types: `RectangleGeographicArea`, `CircleGeographicArea`, and `PolygonGeographicArea`.

```
final RectangleGeographicArea rect = RectangleGeographicArea(
    topLeft: Coordinates(latitude: 1, longitude: 0.5),
    bottomRight: Coordinates(latitude: 0.5, longitude: 1),
);

final CircleGeographicArea circle = CircleGeographicArea(
    centerCoordinates: Coordinates(latitude: 1, longitude: 0.5),
    radius: 100,
);

final PolygonGeographicArea polygon = PolygonGeographicArea(coordinates: [
    Coordinates(latitude: 1, longitude: 0.5),
    Coordinates(latitude: 0.5, longitude: 1),
    Coordinates(latitude: 1, longitude: 1),
    Coordinates(latitude: 1, longitude: 0.5),
]);

alarmService!.monitorArea(rect, id: 'areaRect');
alarmService!.monitorArea(circle, id: 'areaCircle');
alarmService!.monitorArea(polygon, id: 'areaPolygon');
```
Assign a unique identifier to each area. This lets you determine which zone a user has entered or exited.

## Get Monitored Areas
Access active geofences via the `monitoredAreas` getter. It returns a list of `AlarmMonitoredArea` objects containing the parameters you provided to `monitorArea`.

```
List<AlarmMonitoredArea> monitorAreas = alarmService.monitoredAreas;

for (final monitorArea in monitorAreas){
    final GeographicArea area = monitorArea.area;
    final String id = monitorArea.id;
}
```
> 💡 **Tip:** When defining a PolygonGeographicArea, always "close" the shape by making the first and last coordinates identical. Otherwise, the SDK may return polygons that don't match the one you provided.

## Unmonitor an Area
Remove a monitored area by calling the `unmonitorArea` method with the same `GeographicArea` instance you provided to `monitorArea`.

```
final RectangleGeographicArea rect = RectangleGeographicArea(
    topLeft: Coordinates(latitude: 1, longitude: 0.5),
    bottomRight: Coordinates(latitude: 0.5, longitude: 1),
);
alarmService!.monitorArea(rect);

alarmService!.unmonitorArea(rect);
```
You can also use the `unmonitorAreasByIds` method by passing a list of IDs:
```
alarmService.unmonitorAreasByIds(['firstIdToUnmonitor', 'secondIdToUnmonitor'])
```
## Get Notified When Users Enter or Exit Areass
Attach an `AlarmListener` with the `onBoundaryCrossed` callback to your `AlarmService`. This callback returns two arrays: entered area IDs and exited area IDs.

```
AlarmListener(
    onBoundaryCrossed: (List<String> entered, List<String> exited) {
        print("ENTERED AREAS: ${entered}");
        print("EXITED AREAS: ${exited}");
    }
);

alarmService = AlarmService(alarmListener);
```
## Get User Location Areas
Retrieve zones the user is currently inside by calling the `insideAreas` getter:

```
List<AlarmMonitoredArea> insideAreas = alarmService.insideAreas;
```
> 📝 **Note:** For the `insideAreas` getter to return a non-empty list, the user must be inside at least one monitored area and must move or change position within that area.

To retrieve exited zones, call the `outsideAreas` getter.





