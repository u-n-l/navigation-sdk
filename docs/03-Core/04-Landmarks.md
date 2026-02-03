# Landmarks
A landmark is a predefined, permanent location that holds detailed information such as its name, address, description, geographic area, categories (e.g., Gas Station, Shopping), entrance locations, contact details, and sometimes associated multimedia (e.g., icons or images). It represents significant, categorized locations with rich metadata, providing structured context about a place.

## Landmark Structure

### Geographic Details
A landmark's position is defined by `coordinates` (centroid) and `geographicArea` (full boundary). The geographic area can be a circle, rectangle, or polygon. For specific bounding areas, use the `getContourGeographicArea` method.

Calculate the distance between two landmarks using the `distance` method:
```
final double distanceInMeters = landmark1.coordinates.distance(landmark2.coordinates);
```
See the [Coordinates](02-Base%20Entities.md) guide for more details.

### Waypoint Track Data
Some landmarks include a `trackData` attribute representing a sequence of waypoints that outline a path.

Available operations:

- `hasTrackData` - Returns `true` if the landmark contains track data
- `trackData` (getter) - Returns the track as a `Path` object (empty when no track exists)
- `trackData` (setter) - Replaces the landmark's track with a provided `Path`
- `reverseTrackData()` - Reverses the waypoint sequence

Waypoint track data is used for path-based routes. See [Compute path based route](../07-Routing/04-Advanced%20Features.md) for details.

### Descriptive Information
Landmarks include `name`, `description`, and `author` attributes. Names adapt to SDK language settings for localization.

### Categories and Metadata
Landmarks can belong to one or more `categories` (described by `LandmarkCategory`). Use `contactInfo` for phone and email details, and `extraInfo` for additional metadata stored in a structured hashmap.

### Media and Images
Retrieve landmark images using `img` (primary) or `extraImg` (secondary). Validate image data before use.

### Address Information
The `address` attribute connects landmarks to `AddressInfo` for physical address details.

### Store Metadata
Attributes like `landmarkStoreId`, `landmarkStoreType`, and `timeStamp` provide information about the assigned landmark store and insertion time.

### Unique Identifier
The `id` ensures every landmark is uniquely identifiable.

> ⚠️ **Attention:**
> If the `ContactInfo` or `ExtraInfo` object retrieved from a landmark is modified, you must use the corresponding setter to update the value associated with the landmark.
> For example:
> ```
>ContactInfo info = landmark.contactInfo;
>info.addField(type: ContactInfoFieldType.phone, value: '5555551234', name: 'office phone');
>landmark.contactInfo = info; // <-- Does not update the value associated with the landmark without this line
>```

> 🚨 **Alert:** The `ExtraInfo` object also stores data relevant for geographic area, contour geographic area, and Wikipedia information. Modifying `extraInfo` may cause data loss if related fields are not preserved.
> 

## Create Landmarks
Create landmarks using one of these methods:

- **Default**: `Landmark()` - Creates a basic landmark object
- **With coordinates**: `Landmark.withLatLng(latitude, longitude)` - Creates a landmark at specific coordinates
- **With Coordinates object**: L`andmark.withCoordinates(Coordinates coordinates)` - Uses a predefined Coordinates object

> 🚨 **Alert:** Creating a landmark does not automatically display it on the map. See [Display landmarks](../04-Maps/05-Display%20Map/01-Display%20Landmarks.md#display-landmarks) for instructions.

## Interaction with Landmarks
### Select Landmarks
Landmarks are selectable by default. User interactions like taps identify landmarks programmatically using `cursorSelectionLandmarks()`. See [Landmark selection](../04-Maps/04-Interact%20with%20the%20Map.md#select-landmarks) for details.

### Highlight Landmarks
Highlight landmarks to customize their visual appearance. Provide an identifier to activate, deactivate, or update highlights. Updating overrides the previous highlight. See [Highlight landmarks](../04-Maps/05-Display%20Map/01-Display%20Landmarks.md#highlight-landmarks) for details.

### Search Landmarks
Search landmarks by name, location, route proximity, address, and more. Filter searches by landmark categories. See [Get started with Search](../06-Search/02-Get%20Started%20with%20Search.md#getting-started-with-search) for details.

### Calculate Routes
Landmarks are the primary entities for route calculations. See [Get started with Routing](../07-Routing/02-Get%20Started%20wtih%20Routing.md#get-started-with-routing) for details.

### Proximity Alarms
Configure alarms to notify users when approaching specific landmarks. See [Landmarks and overlay alarms](../10-Alarms/04-Landmark%20and%20Overlay%20Alarms.md#landmark-and-overlay-alarms) for implementation details.

### Common Uses
- Map POIs (settlements, roads, addresses, businesses) are landmarks
- Search results return landmark lists
- Route waypoints are landmarks

## Landmark Categories

Landmarks are categorized based on their assigned categories. Each category is defined by a unique ID, an image (which can be used in various UI components created by the SDK user), and a name that is localized based on the language set for the SDK in the case of default categories. Additionally, a landmark may be associated with a parent landmark store if assigned to one.

> 📝 **Note:** A single landmark can belong to multiple categories simultaneously.


### Predefined generic categories
The default landmark categories are presented below:

| Category | Description |
|----------|-------------|
| `gasStation` | Locations where fuel is available for vehicles. |
| `parking` | Designated areas for vehicle parking, including public and private lots. |
| `foodAndDrink` | Places offering food and beverages, such as restaurants, cafes, or bars. |
| `accommodation` | Facilities providing lodging, including hotels, motels, and hostels. |
| `medicalServices` | Healthcare facilities such as hospitals, clinics, and pharmacies. |
| `shopping` | Retail stores, shopping malls, and markets for purchasing goods. |
| `carServices` | Auto repair shops, car washes, and other vehicle maintenance services. |
| `publicTransport` | Locations associated with buses, trains, trams, and other public transit. |
| `wikipedia` | Points of interest with available Wikipedia information. |
| `education` | Educational institutions such as schools, universities, and training centers. |
| `entertainment` | Leisure venues such as cinemas, theaters, or amusement parks. |
| `publicServices` | Government or civic buildings such as post offices and administrative offices. |
| `geographicalArea` | Specific geographical zones or regions of interest. |
| `business` | Office buildings, corporate headquarters, and other business establishments. |
| `sightseeing` | Tourist attractions, landmarks, and scenic points of interest. |
| `religiousPlaces` | Places of worship such as churches, mosques, temples, or synagogues. |
| `roadside` | Amenities located along roads, such as rest areas. |
| `sports` | Facilities for sports and fitness activities, including stadiums and gyms. |
| `uncategorized` | Landmarks that do not fall into a specific category. |
| `hydrants` | Locations of water hydrants, typically for firefighting. |
| `emergencyServicesSupport` | Facilities supporting emergency services, such as dispatch centers. |
| `civilEmergencyInfrastructure` | Infrastructure related to emergency preparedness, such as shelters. |
| `chargingStation` | Stations for charging electric vehicles. |
| `bicycleChargingStation` | Locations for charging bicycles, typically e-bikes. |
| `bicycleParking` | Designated parking areas for bicycles. |


Find category IDs in the `GenericCategoriesId` enum. Use the `getCategory` static method from `GenericCategories` to get the `LandmarkCategory` associated with a `GenericCategoriesId` value.

In addition to the predefined categories, custom landmark categories can be created, offering flexibility to define tailored classifications for specific needs or applications.

### Category Hierarchy
Each generic category can include multiple POI subcategories. The `LandmarkCategory` class is used for both levels.

For example, the Parking category contains subcategories like Park and Ride, Parking Garage, Parking Lot, RV Park, Truck Parking, Truck Stop, and Parking meter.

Retrieve POI subcategories using `GenericCategories.getPoiCategories()`. Find the parent generic category using `GenericCategories.getGenericCategory()`.

>  🚨 **Alert**:
> Important distinction:
> - `getCategory` - Returns `LandmarkCategory` object by ID
> - `getGenericCategory` - Returns parent generic `LandmarkCategory` of a POI subcategory
> 

### Category Uses
- Filter search results by category
- Toggle landmark visibility on the map
- Organize landmarks within stores

## Landmark Stores
Landmark stores are collections of landmarks and categories used throughout the SDK. Each store has a unique `name` and `id`.

Stores persist on device in a SQLite database and remain accessible across sessions.

>  🚨 **Alert**: Landmark coordinates are subject to floating-point precision limitations, which may cause positioning inaccuracies of a few centimeters to meters.

### Manage Landmark Stores
Manage landmark stores using the `LandmarkStoreService` class.

**Create a Landmark Store**

Create a new landmark store:
```
LandmarkStore landmarkStore = LandmarkStoreService.createLandmarkStore('MyLandmarkStore');
```
This method creates a new store or returns an existing one with the same name. Optional parameters include zoom level visibility and custom file path.

>  🚨 **Alert**: Stores persist across sessions. Creating a store with an existing name returns that store, potentially containing previous landmarks and categories.
>

**Get Landmark Store by ID**

Retrieve a store by its ID:
```
LandmarkStore? landmarkStoreById = LandmarkStoreService.getLandmarkStoreById(12345);
```
Returns the `LandmarkStore` object or `null` if the ID doesn't exist.

**Get Landmark Store by Name**

Retrieve a store by name:
```
LandmarkStore? landmarkStoreByName = LandmarkStoreService.getLandmarkStoreByName('MyLandmarkStore');
```
Returns the `LandmarkStore` object or `null` if the name doesn't exist.


**Get All Landmark Stores**

Retrieve all landmark stores:
```
List<LandmarkStore> landmarkStores = LandmarkStoreService.landmarkStores;
```
> 📝 **Note:** This returns both user-created and predefined SDK stores.

**Remove Landmark Stores**

Remove a landmark store:
```
int landmarkStoreId = landmarkStore.id;
landmarkStore.dispose();
LandmarkStoreService.removeLandmarkStore(landmarkStoreId);
```
This removes the store from persistent storage.

> 🚨 **Alert**: 
> **Requirements:**
> - Dispose the store before removing it (undisposed stores will not be removed)
> - Get the store ID before disposing (operations on disposed stores throw exceptions)
> - Ensure the store is not in use (displayed on map or monitored by `AlarmService`)
> If the store is in use, removal fails and ApiErrorService.apiError is set to `GemError.inUse`.

**Get Landmark Store Type**

Retrieve the store type:
```
LandmarkStoreType type = LandmarkStoreService.getLandmarkStoreType(storeId);
```

**Predefined Landmark Stores**

The SDK includes predefined stores:
```
int mapPoisLandmarkStoreId = LandmarkStoreService.mapPoisLandmarkStoreId;
int mapAddressLandmarkStoreId = LandmarkStoreService.mapAddressLandmarkStoreId;
int mapCitiesLandmarkStoreId = LandmarkStoreService.mapCitiesLandmarkStoreId;
```
Use these IDs to determine if a landmark originated from default map elements.

> 🚨 **Alert**: **Do not modify these stores**. They are used for:
> - Filtering landmark categories displayed on the map
> - Checking landmark origin
> - Filtering significant landmarks in search and alarms


**Import Landmarks**

Import landmarks from a file or data buffer into an existing store. Supported formats include KML and GeoJSON. Assign categories and images to imported landmarks. Monitor progress with the returned ProgressListener.
```
ProgressListener? listener = landmarkStore.importLandmarks(
  filePath: '/path/to/file',
  format: LandmarkFileFormat.kml,
  image: landmarkImage,
  onComplete: (GemError error) {
    if (error == GemError.success) {
      // Handle success
    } else {
      // Handle failure
    }
  },
  categoryId: yourCategoryId,
);
```
**Parameters:**

- `filePath` - File path of the landmark file
- `format` - File format (KML, GeoJSON). See `LandmarkFileFormat`
- `image` - Image to associate with landmarks
- `onComplete` - Callback invoked on completion with `GemError`
- `categoryId` - Category ID for imported landmarks (must be valid). Use `uncategorizedLandmarkCategId` for no category

> 🚨 **Alert**: The `categoryId` must be valid.

**Import from Data Buffer**

Import landmarks from a raw data buffer:
```
ProgressListener? listener = landmarkStore.importLandmarksWithDataBuffer(
  buffer: fileBytes,
  format: LandmarkFileFormat.geoJson,
  image: landmarkImage,
  onComplete: (GemError error) {
    if (error == GemError.success) {
      // Handle success
    } else {
      // Handle failure
    }
  },
  categoryId: yourCategoryId,
);
```
**Parameters:**
- `buffer` - Binary data representing the landmark file
- `format` - File format (KML, GeoJSON). See `LandmarkFileFormat`
- `image` - Map image to associate with landmarks
- `onComplete` - Callback triggered on completion with `GemError`
- `categoryId` - Category ID for imported landmarks

> 📝 **Note:** Use this method when receiving data as binary.

### Browse Landmark Stores
Use `LandmarkBrowseSession` to efficiently browse stores with many landmarks.

Create a browse session:
```
LandmarkBrowseSession browseSession = landmarkStore.createLandmarkBrowseSession(
  settings: LandmarkBrowseSessionSettings(
    // Specify the settings here
  ),
);
```
>  🚨 **Alert**: Only landmarks present in the store at session creation are available in the browse session.

**Browse Session Settings:**

| Field Name | Type | Default Value | Description |
|-----------|------|---------------|-------------|
| `descendingOrder` | `bool` | `false` | Specifies whether landmarks are sorted in descending order. By default, sorting is ascending. |
| `orderBy` | `LandmarkOrder` | `LandmarkOrder.name` | Specifies the sorting criteria. By default, landmarks are sorted by name. Other options may include distance or insertion date. |
| `nameFilter` | `String` | empty string | Filters landmarks by name. Only landmarks whose names contain this substring are included. |
| `categoryIdFilter` | `int` | `LandmarkStore.invalidLandmarkCategId` | Filters landmarks by category ID. The default value is invalid, meaning all categories are included. |
| `coordinates` | `Coordinates` | invalid instance | Reference point used when sorting landmarks by distance. Only applicable when `orderBy == LandmarkOrder.distance`. |


**Browse Session Operations:**

| Member | Type | Description |
|--------|------|-------------|
| `id` | `int` (getter) | Returns the unique ID of this session. |
| `landmarkStoreId` | `int` (getter) | Returns the ID of the associated `LandmarkStore`. |
| `landmarkCount` | `int` (getter) | Returns the total number of landmarks in this session. |
| `getLandmarks(int start, int end)` | `List<Landmark>` | Retrieves landmarks in the range `[start, end)`. Useful for pagination or slicing the landmark list. |
| `getLandmarkPosition(int landmarkId)` | `int` | Returns the zero-based index of the landmark with the given ID, or a not-found value. |
| `settings` | `LandmarkBrowseSessionSettings` (getter) | Returns the current session settings. Modifying this object does not affect the session. |


### Landmark Store Operations
The `LandmarkStore` class provides these operations:

| Operation | Description |
|----------|-------------|
| `addCategory(LandmarkCategory category)` | Adds a new category to the store. The category must have a name. After addition, the category belongs to this store. |
| `addLandmark` | Adds a copy of a landmark to a specified category in the store. If the landmark already exists, the category information is updated. Defaults to `uncategorized` if no category is specified. |
| `getLandmark(int landmarkId)` | Retrieves the landmark with the specified ID from the store. Returns `null` if the landmark does not exist. |
| `updateLandmark` | Updates information about an existing landmark in the store. This does not change the landmark’s category. The landmark must belong to this store. |
| `containsLandmark` | Checks whether the store contains a landmark with the specified ID. Returns `true` if found, otherwise `false`. |
| `categories` | Returns a list of all categories in the store. |
| `getCategoryById` | Retrieves a category by its ID. Returns `null` if the category is not found. |
| `getLandmarks` | Retrieves a list of landmarks in a specified category. Defaults to all categories if none is specified. |
| `removeCategory` | Removes a category by its ID. Optionally removes the landmarks in the category or marks them as uncategorized. |
| `removeLandmark` | Removes a specific landmark from the store. |
| `updateCategory` | Updates the details of an existing category. The category must belong to this store. |
| `removeAllLandmarks` | Removes all landmarks from the store. |
| `id` | Returns the unique ID of the landmark store. |
| `name` | Returns the name of the landmark store. |
| `type` | Returns the type of the landmark store. Possible values include `none`, `defaultType`, `mapAddress`, `mapPoi`, `mapCity`, `mapHighwayExit`, and `mapCountry`. |


### Landmark Store Uses
- Display landmarks on the map
- Customize search functionality
- Manage proximity alarms
- Persist landmarks across sessions
