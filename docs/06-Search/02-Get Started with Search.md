# Getting Started with Search
The Navigation SKD provides flexible search functionality for finding locations using text queries and coordinates:

- **Text Search** — perform searches using a text query and geographic coordinates to prioritize results within a specific area
- **Search Preferences** — customize search behavior using options such as fuzzy results, distance limits, or re**sult count
- Category-Based Search** — filter search results by predefined categories, such as gas stations or parking areas
- **Proximity Search** — retrieve all nearby landmarks without specifying a text query

## Text Search
Search by providing text and coordinates. The coordinates serve as a hint, prioritizing points of interest (POIs) within the indicated area.

```
const text = "Paris";
final coords = Coordinates(latitude: 45, longitude: 10);
final preferences = SearchPreferences(
  maxMatches: 40,
  allowFuzzyResults: true,
);

TaskHandler? taskHandler = SearchService.search(
  text,
  coords,
  preferences: preferences,
  (err, results) async {
    // If there is an error or there aren't any results, the method will return an empty list.
    if (err == GemError.success) {
      if (results.isEmpty) {
        showSnackbar("No results");
      } else {
        showSnackbar("Number of results: ${results.length}");
      }
    } else {
      showSnackbar("Error: $err");
    }
  },
);
```
> 📝 **Note:** The `SearchService.search` method returns `null` only when the geographic search fails to initialize. In such cases, calling `SearchService.cancelSearch(taskHandler)` is not possible. Error details are delivered through the `onComplete` function of the `SearchService.search` method.

The `err` provided by the callback function can have the following values:

| Value | Significance |
|-------|--------------|
| `GemError.success` | Successfully completed |
| `GemError.cancel` | Cancelled by the user |
| `GemError.noMemory` | Search engine couldn't allocate the necessary memory for the operation |
| `GemError.operationTimeout` | Search was executed on the online service and the operation took too much time to complete (usually more than 1 min, depending on the server overload state) |
| `GemError.networkTimeout` | Can't establish the connection or the server didn't respond on time |
| `GemError.networkFailed` | Search was executed on the online service and the operation failed due to bad network connection |


## Specify Preferences
Before searching, specify `SearchPreferences` to customize search behavior:

| Field | Type | Default Value | Explanation |
|------|------|---------------|-------------|
| allowFuzzyResults | bool | true | Allows fuzzy search results, enabling approximate matches for queries. |
| estimateMissingHouseNumbers | bool | true | Enables estimation of missing house numbers in address searches. |
| exactMatch | bool | false | Restricts results to only those that exactly match the query. |
| maxMatches | int | 40 | Specifies the maximum number of search results to return. |
| searchAddresses | bool | true | Includes addresses in the search results. This option also includes roads. |
| searchMapPOIs | bool | true | Includes points of interest (POIs) on the map in the search results. |
| searchOnlyOnboard | bool | false | Limits the search to onboard (offline) data only. |
| thresholdDistance | int | 2147483647 | Defines the maximum distance (in meters) for search results from the query location. |
| easyAccessOnlyResults | bool | false | Restricts results to locations that are easily accessible. |


### Search by Category

Filter search results based on categories.

Use the `GenericCategories.getCategory` static method to get a specific category by its identifier. The predefined category IDs are available in the `GenericCategoriesId` enum.

The following example performs a search, limiting the results to the "Food & Drink" and "Entertainment" categories.

```
final coords = Coordinates(latitude: 45, longitude: 10);
final preferences = SearchPreferences(
  maxMatches: 40,
  allowFuzzyResults: true,
  // Without the following line, other unrelated results that represent addresses may also be returned
  searchAddresses: false,
  // Without the following line, other unrelated results that represent POIs may also be returned
  searchMapPOIs: false,
);

final LandmarkCategory foodAndDrinkCategory = GenericCategories.getCategory(GenericCategoriesId.foodAndDrink.id)!;
final LandmarkCategory entertainmentCategory = GenericCategories.getCategory(GenericCategoriesId.entertainment.id)!;

final GemError error1 =
    preferences.landmarks.addStoreCategoryId(
  foodAndDrinkCategory.landmarkStoreId,
  foodAndDrinkCategory.id,
);
final GemError error2 =
    preferences.landmarks.addStoreCategoryId(
  entertainmentCategory.landmarkStoreId,
  entertainmentCategory.id,
);

TaskHandler? taskHandler = SearchService.searchAroundPosition(
  coords,
  preferences: preferences,
  (err, results) async {
    if (err == GemError.success) {
      if (results.isEmpty) {
        showSnackbar("No results");
      } else {
        showSnackbar("Number of results: ${results.length}");
      }
    } else {
      showSnackbar("Error: $err");
    }
  },
);
```
> 💡 **Tip:** Set `searchAddresses` and `searchMapPOIs` to false to filter non-relevant results.

The complete list of predefined categories is available via the static `GenericCategories.categories` getter, which returns a `List<LandmarkCategory>` collection.

The `addStoreCategoryId` method returns a `GemError` value:

- `GemError.success` — the category was added successfully
- `GemError.notFound` — the specified category or landmark store does not exist

Use the modified `SearchPreferences` with custom categories in all search methods.

### Search on Custom Landmarks
By default, all search methods operate on the landmarks provided on the map. Enable search functionality for custom landmarks by creating a landmark store containing the desired landmarks and adding it to the search preferences.

```
// Create the landmarks to be added
Landmark landmark1 = Landmark()
  ..coordinates = Coordinates(latitude: 25, longitude: 30)
  ..name = "My Custom Landmark1";

Landmark landmark2 = Landmark()
  ..coordinates = Coordinates(latitude: 25.005, longitude: 30.005)
  ..name = "My Custom Landmark2";

// Create a store and add the landmarks
LandmarkStore store = LandmarkStoreService.createLandmarkStore('LandmarksToBeSearched');
store.addLandmark(landmark1);
store.addLandmark(landmark2);

// Add the store to the search preferences
SearchPreferences preferences = SearchPreferences();
preferences.landmarks.add(store);

// If no results from the map POIs should be returned then searchMapPOIs should be set to false
preferences.searchMapPOIs = false;

// If no results from the addresses should be returned then searchAddresses should be set to false
preferences.searchAddresses = false;

// Search for landmarks
SearchService.search(
  "My Custom Landmark",
  Coordinates(latitude: 25.003, longitude: 30.003),
  preferences: preferences,
  (err, results) {
    if (err == GemError.success) {
      if (results.isEmpty) {
        showSnackbar("No results");
      } else {
        showSnackbar("Number of results: ${results.length}");
      }
    } else {
      showSnackbar("Error: $err");
    }
  },
);
```
>  🚨 **Alert**: The landmark store retains the landmarks added to it across sessions until the app is uninstalled. A previously created landmark store with the same name might already exist in persistent storage and may contain pre-existing landmarks. For more details, refer to the [documentation on LandmarkStore](../03-Core/04-Landmarks.md#landmark-stores).

> 💡 **Tip:** Set `searchAddresses` and `searchMapPOIs` to false to filter non-relevant results.

### Search on Overlays
Perform searches on overlays by specifying the overlay ID. Consult the [Overlay documentation](/03-Core/06-Overlays.md) for more details about proper usage.

The example below demonstrates how to search within items from the safety overlay. Custom overlays can also be used if they are activated in the applied map style:

```
// Get the overlay id of safety
int overlayId = CommonOverlayId.safety.id;

// Add the overlay to the search preferences
SearchPreferences preferences = SearchPreferences();
preferences.overlays.add(overlayId);

// We can set searchMapPOIs and searchAddresses to false if no results from the map POIs and addresses should be returned
preferences.searchMapPOIs = false;
preferences.searchAddresses = false;

TaskHandler? taskHandler = SearchService.search(
  "Speed",
  Coordinates(latitude: 48.76930, longitude:  2.34483),
  preferences: preferences,
  (err, results) {
    if (err == GemError.success) {
      if (results.isEmpty) {
        showSnackbar("No results");
      } else {
        mapController.centerOnCoordinates(results.first.coordinates);
        showSnackbar("Number of results: ${results.length}");
      }
    } else {
      showSnackbar("Error: $err");
    }
  },
);
```
To convert the returned `Landmark` to an `OverlayItem`, use the `overlayItem` getter of the `Landmark` class. This method returns the associated `OverlayItem` if available; otherwise, it returns `null`.

> 💡 **Tip:** Set `searchAddresses` and `searchMapPOIs` to `false` to filter non-relevant results.

>  🚨 **Alert**: Overlay search requires a `GemMap` with a style that includes the overlay being searched. If the map is not initialized or the overlay is not part of the current map style, the `preferences.overlays.add` operation will fail with a `GemError.notFound` error, and the search will return `GemError.invalidInput` with no results. The default map style includes all common overlays.


## Search for Location
Without specifying text, all landmarks in the closest proximity are returned, limited to `maxMatches`.

```
final coords = Coordinates(latitude: 45, longitude: 10);
final preferences = SearchPreferences(
  maxMatches: 40,
  allowFuzzyResults: true,
);

TaskHandler? taskHandler = SearchService.searchAroundPosition(
  coords,
  preferences: preferences,
  (err, results) async {
    // If there is an error or there aren't any results, the method will return an empty list.
    if (err == GemError.success) {
      if (results.isEmpty) {
        showSnackbar("No results");
      } else {
        showSnackbar("Number of results: ${results.length}");
      }
    } else {
      showSnackbar("Error: $err");
    }
  },
);
```
To limit the search to a specific area, provide a `GeographicArea` such as a `RectangleGeographicArea` instance to the optional `locationHint` parameter.

```
final coords = Coordinates(latitude: 41.68905, longitude: -72.64296);

final searchArea = RectangleGeographicArea(
    topLeft: Coordinates(latitude: 41.98846, longitude: -73.12412),
    bottomRight: Coordinates(latitude: 41.37716, longitude: -72.02342));

SearchService.search('N',
  coords,
  (err, result) {
    successfulSearchCompleter.complete((err, result));
  },
  preferences: SearchPreferences(maxMatches: 400),
  locationHint: searchArea,
);
```
>  🚨 **Alert**: The reference coordinates used for search must be located within the `GeographicArea` provided to the `locationHint` parameter. Otherwise, the search will return an empty list.


## Show Results On the Map
In most use cases, the landmarks found by search are already present on the map. If the search was made on custom landmark stores, see the [add map landmarks](../04-Maps/05-Display%20Map/01-Display%20Landmarks.md#add-custom-landmarks) section for adding landmarks to the map.

To zoom to a landmark found via search, use `GemMapController.centerOnCoordinates` on the coordinates of the landmark found (`Landmark.coordinates`). See the documentation for [map centering](../04-Maps/03-Adjust%20Map%20View.md#adjust-the-map-view) for more information.









