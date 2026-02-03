# Social Reports
Social reports are user-generated alerts about real-time driving conditions or incidents on the road, including accidents, police presence, road construction, and more.

Users can create reports with category, name, image, and other parameters. They can vote on accuracy, comment on events, confirm or deny validity, and delete their own reports. Social reports are visible to all users with the social overlay enabled and a compatible map style.

## Report categories
The following categories and subcategories are provided in the form of a hierarchical structure, based on the `OverlayCategory` class:

```
┌ Police Car (id 256)
│   - My Side (id 264)
│   - Opposite Side (id 272)
│   - Both Sides (280)
└■
┌ Fixed Camera (id 512)
│   - My Side (id 520)
│   - Opposite Side (id 528)
│   - Both Sides (536)
└■
┌ Traffic (id 768)
│   - Moderate (id 776)
│   - Heavy (id 784)
│   - Standstill (792)
└■
┌ Crash (id 1024)
│   - My Side (id 1032)
│   - Opposite Side (id 1040)
└■
┌ Crash (id 1024)
│   - My Side (id 1032)
│   - Opposite Side (id 1040)
└■
┌ Road Hazard (id 1280)
│   - Pothole (id 1288)
│   - Constructions (id 1296)
│   - Animals (id 1312)
│   - Object on Road (id 1328)
│   - Vehicle Stopped on Road (id 1344)
└■
┌ Weather Hazard (id 1536)
│   - Fog (id 1544)
│   - Ice on Road (id 1552)
│   - Flood (id 1560)
│   - Hail (id 1568)
└■
┌ Road Closure (id 3072)
│   - My Side (id 3080)
│   - Opposite Side (id 3088)
│   - Both Sides (id 3096)
└■
```
The main categories and subcategories can be retrieved via the following snippet:

```
final List<OverlayCategory> categories = SocialOverlay.reportsOverlayInfo.categories;

for (final OverlayCategory category in categories){
    print("Category name: ${category.name}");
    print("Category id: ${category.uid}");
    
    for (final OverlayCategory subCategory in category.subcategories){
        print("Subcategory name: ${subCategory.name}");
        print("Subcategory id: ${subCategory.uid}");
    }
}
```
More details about the OverlayCategory class structure can be found in the [Overlay documentation](/03-Core/06-Overlays.md).

## Step 1: Prepare and Upload a Report
Before uploading a social report, prepare it first. The `SocialOverlay` class provides methods like `prepareReporting` and `prepareReportingCoords` to handle the report preparation phase.

The `prepareReporting` method takes a category ID and uses the current user's location or a specified data source, while prepareReportingCoords accepts both a category ID and a `Coordinates` entity, enabling reporting from a different location. These methods return an integer called `prepareId`, which is passed to the report method to upload a social overlay item.

Prepare and report a social overlay item:
```
// Get the reporting id (uses current position)
int idReport = SocialOverlay.prepareReporting(categId: 0);

// Get the subcategory id
SocialReportsOverlayInfo info = SocialOverlay.reportsOverlayInfo;
List<SocialReportsOverlayCategory> categs = info.getSocialReportsCategories();
SocialReportsOverlayCategory cat = categs.first;
List<SocialReportsOverlayCategory> subcats = cat.overlaySubcategories;
SocialReportsOverlayCategory subCategory = subcats.first;

// Report
EventHandler? handler = SocialOverlay.report(
    prepareId: idReport,
    categId: subCategory.uid,
    onComplete: (error) {
        print("Report result error: $error");
    },
);
```
If you want to use a location from a specific data source, you can pass the data source to the `prepareReporting` method, as shown below:

```
// Assuming 'ds' is an instance of a valid DataSource
int idReport = SocialOverlay.prepareReporting(dataSource: ds, categId: 0);
```
>  🚨 **Alert**: `prepareReporting` must be called with a `DataSource` whose position data is classified as **high accuracy** by the map-matching system (the only exception is the `Weather Hazard` category, `categId = 1536`, which does not require accurate positioning). If a high-accuracy data source is not provided, the method returns `GemError.notFound` and the report cannot be created.

The report is displayed for a limited duration before being automatically removed.

The report result is provided via the `onComplete` callback with the following `GemError` values:

- `invalidInput` - Category ID is invalid, parameters are ill-formatted, or snapshot is an invalid image
- `suspended` - Rate limit for the user is exceeded
- `expired` - Prepared report is too old
- `notFound` - No accurate data source is detected
- `success` - Operation succeeded

The method returns an `EventHandler` instance, which can be used to cancel the operation by calling the static cancel method from the `SocialOverlay` class (applicable for other operations such as upvote, downvote, update, etc.).
If the operation could not be started, the method returns `null`.

>  🚨 **Alert**: Most report categories require the `prepareReporting` method to ensure higher report accuracy by confirming the user's proximity to the reported location. See the [Get started with Positioning guide](05-Positioning%20&%20Sensors/03-Get%20Started%20wtih%20Positioning.md#get-started-with-positioning) for more information about configuring the data source.

The `prepareReportingCoords` method works only for `Weather Hazard` categories and subcategories contained within.

>  🚨 **Alert**: While reporting events, the `prepareReporting` method needs to be in preparing mode (categId=0) rather than dry run mode (categId !=0).

> 💡 **Tip:** The `report` function accepts the following optional parameters:
>
> - `snapshot` and `format` - Provide an image for the report. `snapshot` may refer to a file path or image data, and `format` specifies the image type (e.g., "`png`" or "`jpeg`")
> - `params` - A `ParameterList` configuration object for further customization of report details
>
> These parameters are optional and can be omitted if not needed.

## Step 2: Update a Report
Update an existing report's parameters using the `SocialOverlay.updateReport(item, params)` method:

```
List<OverlayItem> overlays = mapController.cursorSelectionOverlayItems();

SearchableParameterList params = overlays.first.previewDataParameterList;
GemParameter param = params.findParameter("location_address");
param.value = "New address";

final handler = SocialOverlay.updateReport(
    item: overlays.first,
    params: params,
    onComplete: (GemError error) {
        print("Update result error: $error");
    },
);
```
The structure of the `SearchableParameterList` object passed to the update method should follow the structure returned by the `OverlayItem`'s `previewData`. The keys of the fields accepted can be found inside `PredefinedOverlayGenericParametersIds` and `PredefinedReportParameterKeys`.

The report method provides the following `GemError` values via the `onComplete` callback:

- `invalidInput` - `SearchableParameterList`'s structure is incorrect
- `success` - Operation completed successfully

> 💡 **Tip:** A user can obtain a report `OverlayItem` through the following methods:
>
> - **Map Selection** - Select an item directly from the map using the `cursorSelectionOverlayItems` method provided by the `GemMapController`
> - **Search** - Perform a search that includes preferences configured to return overlay items
> - **Proximity Alerts** - Via the `AlarmListener`, when approaching a report that triggers an alert

## Step 3: Delete a Report
Delete a report using `SocialOverlay.deleteReport(overlayItem)`. Only the original creator of the report has the authority to delete it.

The `delete` method provides the following `GemError` values on the `onComplete` method:

- `invalidInput` - Item is not a social report overlay item or not the result of an alarm notification
- `accessDenied` - User does not have the required rights
- `success` - Operation completed successfully

## Step 4: Interact with Reports
Provide positive feedback
Provide positive feedback for a reported event using `SocialOverlay.confirmReport()`, which increases the score value within `OverlayItem.previewData`.

### Provide negative feedback
Deny inaccurate reports using `SocialOverlay.denyReport()`, which accepts an `OverlayItem` object as its parameter. Reports with many downvotes are removed automatically.

Both `confirmReport` and `denyReport` provide the following `GemError` values using the `onComplete` callback:

- `invalidInput` - Item is not a social report overlay item or not the result of an alarm notification
- `accessDenied` - User already voted
- `success` - Operation completed successfully

### Add comment
Contribute comments to a reported event using `SocialOverlay.addComment`:

```
final handler = SocialOverlay.addComment(
    item: overlay,
    comment: "This is a comment",
    onComplete: (GemError error) {
        print("Add comment result error: $error");
    },
);
```
Added comments can be viewed within the `OverlayItem`'s previewData.

The `addComment` method provides the following `GemError` values on the `onComplete` callback:

- `invalidInput` - Item is not a social report overlay item or not the result of an alarm notification
- `connectionRequired` - No internet connection is available
- `busy` - Another comment operation is in progress
- `success` - Comment was added

## Step 5: Get Updates About a Report
Track changes to a report using the `SocialReportListener` class.

Create and register a listener for a specific `OverlayItem` report:

```
SocialReportListener listener = SocialReportListener(
    onReportUpdated: (OverlayItem report) {
    print('The report has been updated');
    },
);

GemError error = SocialOverlay.registerReportListener(overlay, listener);
if (error != GemError.success) {
    print('The register failed');
}
```
The `registerReportListener` method returns the following possible values:

- `GemError.success` - Listener successfully registered
- `GemError.invalidInput` - Provided `OverlayItem` is not a social overlay item
- `GemError.exist` - Listener already registered for the report

Unregister the listener:

```
GemError error = SocialOverlay.unregisterReportListener(overlay, listener);
if (error != GemError.success) {
    print('The unregister failed');
}
```
The `unregisterReportListener` method returns the following possible values:

- `GemError.success` - Listener successfully removed
- `GemError.invalidInput` - Provided OverlayItem is not a social overlay item
- `GemError.notFound` - Listener was not registered for the report

