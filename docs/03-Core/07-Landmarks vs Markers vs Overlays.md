# Landmarks vs Markers vs Overlays
When building a sophisticated mapping application, choosing the right type of object to use for your specific needs is crucial. To assist in making an informed decision, we compare the three core mapping entities in the table below:

| Characteristic | Landmarks | Markers | Overlays |
|---------------|-----------|---------|----------|
| Select from map | Basic selection using `cursorSelectionLandmarks()` | Advanced selection using `cursorSelectionMarkers()`, providing the matched marker, its collection, and positional details (such as index within the collection, hit segment or vertex, etc.) | Basic selection using `cursorSelectionOverlayItems()` |
| On the map by default | Yes | No | Yes, if present within the style |
| Customizable render settings | Basic customization using highlights | High level of customization using `MarkerRenderSettings` | Defined within the style (in Studio). Also supports customization using highlights |
| Visibility and layering | Toggleable based on category and store | Can be changed individually | Toggleable based on category and overlay |
| Searchable | Yes | No | Yes |
| Can be used for route calculation | Yes | No | No |
| Can be used for alarms | Yes | No | Yes |
| Create custom items | Programmatically within the client application | Programmatically within the client application | Using uploaded GeoJSON data sets (in Studio); cannot be created within the client application¹ |
| Available offline | Yes | Yes | No, with some exceptions |
| Shared among users | Yes, for predefined landmarks only. Changes to landmarks and custom landmarks are local | No | Yes, overlay items are accessible to all users with the correct style applied |
| Extra info | Address, contact info, category, etc. | No | Flexible data structure (`SearchableParameterList`) |

> 📝 **Note:** Social reports can be created and modified by app clients and are accessible to all other users.


