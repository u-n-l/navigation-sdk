# Projections
 The SDK provides a Projection class that represents the base class for different geocoordinate systems.

 ## Supported Projection Types
- `WGS84` - World Geodetic System 1984
- `GK` - Gauss-Kruger
- `UTM` - Universal Transverse Mercator
- `LAM` - Lambert
- `BNG` - British National Grid
- `MGRS` - Military Grid Reference System
- `W3W` - What three words

You can check the projection `type` using the type getter:
```
final type = projection.type;
```
## WGS84 Projection
The WGS84 projection is a widely used geodetic datum that serves as the foundation for GPS and other mapping systems.

Create a `WGS84` projection using a `Coordinates` object:
```
final obj = WGS84Projection(Coordinates(latitude: 5.0, longitude: 5.0));
```

Access and modify coordinates using the `coordinates` getter and setter:

```
final coordinates = obj.coordinates; // Get coordinates
obj.coordinates = Coordinates(latitude: 10.0, longitude: 10.0); // Set coordinates
```
> 📝 **Note:** The coordinates getter returns null if the coordinates are not set.

## GK Projection
The Gauss-Kruger projection is a cylindrical map projection commonly used for large-scale mapping in regions with a north-south orientation. It divides the Earth into zones, each with its own coordinate system.

Create a `Gauss-Kruger` projection:
```
final obj = GKProjection(x: 6325113.72, y: 5082540.66, zone: 1);
```
Access values using the `easting`, `northing` and `zone` getters. Modify them using the `setFields` method:
```
final obj = GKProjection(x: 6325113.72, y: 5082540.66, zone: 1);

final type = obj.type; // ProjectionType.gk
final zone = obj.zone; // 1
final easting = obj.easting; // 6325113.72
final northing = obj.northing; // 5082540.66

obj.setFields(x: 1, y: 1, zone: 2);

final newZone = obj.zone; // 2
final newEasting = obj.easting; // 1
final newNorthing = obj.northing; // 1
```
>  🚨 **Alert**: The `Gauss-Kruger` projection is currently supported only for countries that use Bessel ellipsoid. Converting to and from `Gauss-Kruger` projection for other countries will result in a `GemError.notSupported` error.

## BNG Projection
The BNG (British National Grid) projection is a coordinate system used in Great Britain for mapping and navigation. It provides a grid reference system for precise location identification.

Create a `BNG` projection:
```
final obj = BNGProjection(easting: 500000, northing: 4649776);
```
Access values using the `easting` and `northing` getters. Modify them using the `setFields` method:
```
final obj = BNGProjection(easting: 6325113.72, northing: 5082540.66);

obj.setFields(easting: 1, northing: 1);

final type = obj.type; // ProjectionType.bng
final newEasting = obj.easting; // 1
final newNorthing = obj.northing; // 1
```

## MGRS Projection
The MGRS (Military Grid Reference System) projection is a coordinate system used by the military for precise location identification. It combines the UTM and UPS coordinate systems.

Create a `MGRS` projection:
```
final obj = MGRSProjection(easting: 99316, northing: 10163, zone: '30U', letters: 'XC');
```
Access values using the `easting`, `northing`, `zone` and `letters` getters. Modify them using the `setFields` method:

```
final obj = MGRSProjection(
    easting: 6325113, northing: 5082540, zone: 'A', letters: 'letters');

obj.setFields(
    easting: 1, northing: 1, zone: 'B', letters: 'newLetters');

final type = obj.type; // ProjectionType.mgrs
final newZone = obj.zone; // B
final newEasting = obj.easting; // 1
final newNorthing = obj.northing; // 1
final newLetters = obj.letters; // newLetters
```
## LAM Projection
The LAM (Lambert) projection is a conic map projection commonly used for large-scale mapping in regions with an east-west orientation.

Create a `LAM` projection:
```
final obj = LAMProjection(x: 6325113.72, y: 5082540.66);
```
Access values using the `x` and `y` getters. Modify them using the `setFields` method.
```
final obj = LAMProjection(x: 6325113.72, y: 5082540.66);

obj.setFields(x: 1, y: 1);

final type = obj.type; // ProjectionType.lam
final newX = obj.x; // 1
final newY = obj.y; // 1
```

## UTM Projection
The UTM (Universal Transverse Mercator) projection is a global map projection that divides the world into a series of zones, each with its own coordinate system.

Create a `UTM` projection:
```
final obj = UTMProjection(x: 6325113.72, y: 5082540.66, zone: 1, hemisphere: Hemisphere.south);
```
Access values using the `x`, `y`, `zone` and `hemisphere` getters. Modify them using the `setFields` method:

```
final obj = UTMProjection(x: 6325113.72, y: 5082540.66, zone: 1, hemisphere: Hemisphere.south);

obj.setFields(x: 1, y: 1, zone: 2, hemisphere: Hemisphere.north);

final type = obj.type; // ProjectionType.utm
final newZone = obj.zone; // 2
final newX = obj.x; // 1
final newY = obj.y; // 1
final newHemisphere = obj.hemisphere; // Hemisphere.north
```
## Convert Between Projections

The `ProjectionService` class provides a method to convert between different projection types. Use the `static convert` method to transform coordinates from one projection to another:

```
final from = WGS84Projection(Coordinates(latitude: 51.5074, longitude: -0.1278));
final toType = ProjectionType.mgrs;

final completer = Completer<Projection?>();
ProjectionService.convert(
    from: from,
    toType: toType,
    onComplete: (error, result) {
        if (error == GemError.success) {
            completer.complete(result);
        } else {
            completer.completeError(error);
        }
    },
);

final result = await completer.future;
final mgrs = result as MGRSProjection;

final easting = mgrs.easting; // 99316
final northing = mgrs.northing; // 10163
final zone = mgrs.zone; // 30U
final letters = mgrs.letters; // XC
```
