# Display Overlays
Learn how to enable and disable map overlays to add or remove contextual information layers.

## Overview
Overlays provide enhanced, layered information on top of your base map. They offer dynamic, interactive, and customized data that adds contextual value to map elements.

## Disable Overlays
Disable overlays by applying a custom map style from UNL with certain overlays disabled, or by using the `disableOverlay` method:
```
GemError error = OverlayService.disableOverlay(CommonOverlayId.publicTransport.id);
```

Pass -1 (default value) as the optional `categUid` parameter to disable the entire overlay rather than a specific category.

The returned error is `success` if the overlay was disabled, or `notFound` if no overlay with the specified ID was found in the applied style.

### Disable specific overlay categories
To disable specific overlays within a category, retrieve their unique identifiers (uid):
```
final Completer<GemError> completer = Completer<GemError>();
final availableOverlays = OverlayService.getAvailableOverlays(onCompleteDownload: (error) {
  completer.complete(error);
});

await completer.future;
  
final collection = availableOverlays.first;
final overlayInfo = collection.getOverlayAt(0);
if(overlayInfo != null) {
  final uid = overlayInfo.uid;
  final err = OverlayService.disableOverlay(uid);
  showSnackbar(err.toString());
}
```

## Enable Overlays
In a similar way, the overlay can be enabled using the `enableOverlay` method by providing the overlay id. It also has an optional `categUid` parameter, which when left as default, it activates whole overlay rather than a specific category.
