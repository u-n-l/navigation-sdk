# Route Bookmarks
This guide explains how to store, manage, and retrieve route collections as bookmarks between application sessions.

## Create a Bookmarks Collection
Create a new bookmarks collection using the `RouteBookmarks.create` method with a unique name:

```
final bookmarks = RouteBookmarks.create('my_trips');
```
If a collection with the same name exists, it opens the existing collection.

Access the file path using the `filePath` property:
```
String path = bookmarks.filePath;
```
## Add Routes
Add a route to the collection using the `add` method. Provide a unique name and waypoints list. Optionally include route preferences and specify whether to overwrite existing routes.
```
bookmarks.add(
  'Home to Office',
  [homeLandmark, officeLandmark],
  preferences: myPreferences,
  overwrite: false,
);
```
**Parameters:**

- `name` - Unique route name
- `waypoints` - List of landmarks defining the route
- `preferences` - Optional route preferences
- `overwrite` - Replace existing route with same name (default: false)

> 📝 **Note:** If a route with the same name exists and `overwrite` is `false`, the operation fails.

## Import Routes From Files
Import multiple routes from a file using addTrips. Returns the number of imported routes or `GemError.invalidInput.code` on failure.

```
final int count = bookmarks.addTrips('/path/to/bookmarks_file');
if (count == GemError.invalidInput.code){
    showSnackbar('Invalid file path provided for import.');
} else {
    showSnackbar('$count trips imported successfully.');
}
```
## Export Routes To Files
Export a specific route to a file using `exportToFile` with the route index and destination path.

```
final result = bookmarks.exportToFile(0, '/path/to/exported_route');
showSnackbar('Export completed with result: $result');
```
**Return values:**

- `GemError.success` - Export successful
- `GemError.notFound` - Route does not exist
- `GemError.io` - File cannot be created


## Access Route Details
Get the number of routes in the collection using the `size` property:

```
final int count = bookmarks.size;
```
Get details of a specific route by index:

```
String? name = bookmarks.getName(0);
List<Landmark>? waypoints = bookmarks.getWaypoints(0);
RoutePreferences? prefs = bookmarks.getPreferences(0);
DateTime? timestamp = bookmarks.getTimestamp(0);
```
> 📝 **Note:** Methods return `null` if the index is out of bounds or data is unavailable. The `getTimestamp` method returns when the route was added or modified.

Find the index of a route by name using the `find` method:
```
final int index = bookmarks.find('Home to Office');
if (index >= 0) {
    showSnackbar('Route found at index $index.');
} else {
    showSnackbar('Error finding route: $index');
}
```
**Return values:**

- Route index if found (positive value)
- `GemError.notFound.code` if not found

### Sort bookmarks
Change the sort order using the `sortOrder` property:

**Available sort orders:**

- `RouteBookmarksSortOrder.sortByDate` (default) - Most recent first
- `RouteBookmarksSortOrder.sortByName` - Alphabetical order

### Configure auto-delete mode
Enable or disable auto-delete mode using the `autoDeleteMode` property. When enabled, the bookmarks database is deleted when the object is destroyed.

## Update Routes
Update an existing route using the `update` method with the route index and new details:

```
bookmarks.update(
    0,
    name: 'New Name',
    waypoints: [newStart, newEnd],
    preferences: newPrefs,
);
```
> 📝 **Note:** The `update` method only modifies provided fields, leaving others unchanged.

## Remove Routes
Remove a route by index using the `remove` method:
```
bookmarks.remove(0);
```
Clear all routes from the collection using the `clear` method:

```
bookmarks.clear();
```


