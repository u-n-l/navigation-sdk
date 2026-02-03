# Camera Feed
The Navigation SDK `DataSource` provides access to recorded and live frames from the device camera for both live camera feeds and recorded logs.
> 💡 **Tip:**  Recorded logs with camera data are MP4 files that can be played back using standard video players. Using external players such as [video_player](/link:https://pub.dev/packages/video_player) is the preferred approach for most log-playback scenarios, as they provide better performance.

## Camera Player Widget
The `GemCameraPlayer` widget displays the camera feed on screen. Wrap the widget in a SizedBox or similar to control its size.

### Parameters
- `controller` — the `GemCameraPlayerController` that drives playback and provides camera frames (required). Connects the widget to the source of frames, exposes playback status (loading, playing, error, ended), and is used internally to start/stop rendering and request frame updates
- `fallbackWidget` — widget shown when a frame cannot be decoded or an error state is reported by the controller
- `loadingWidget` — widget shown while the player is waiting for the first frame or is in a loading state
- `endWidget` — widget shown once the camera feed or recording has finished and the controller reports an ended state
- `fit` — `BoxFit` describing how the decoded image should be sized into the available space. Defaults to cover-like behavior. See the [BoxFit API reference](/link:https://api.flutter.dev/flutter/painting/BoxFit.html) for more information

## Camera Player Controller
The `GemCameraPlayerController` is the main interface for controlling camera feed playback and accessing frames. Create it via the constructor with a `DataSource` as the required parameter. Optionally, provide a `CameraConfiguration` to the `configurationOverride` parameter to customize the frame size.

>  🚨 **Alert**: The `DataSource` provided to the `GemCameraPlayerController` must have the `DataType.camera` data type enabled. Providing a `DataSource` without camera data will result in an error state in the controller and cause the `fallbackWidget` to be shown in the `GemCameraPlayer`.

>  🚨 **Alert**: When presenting the live camera feed, a video recording session must be active on the `DataSource`. See the [Video Recording guide](06-Recorder.md#record-video) for more information on starting a video recording session.

### Properties
- `datasource` — the `DataSource` provided during construction. Use it to query additional information about the source of camera frames and to control the playback session
- `size` — the size of the camera frames being provided by the controller, taking into account any configuration overrides
- `status` — the current playback status of the controller:
  - `loading` — the controller is waiting for the first frame to be available
  - `error` — an error occurred while trying to provide frames (for example, the data source does not provide camera data or the raw camera frame data is in an unsupported format)
  - `playing` — the controller is actively providing frames
  - `paused` — the controller has paused frame delivery
  - `ended` — the camera feed or recording has finished
- `camera` — the `Camera` instance providing camera parameters such as intrinsics and extrinsics. Returns `null` if no camera data is available from the `DataSource`

### Pause and Resume
- `pause()` — pauses the camera feed. The controller's status changes to `paused`
- `resume()` — resumes the camera feed if it was paused. The controller's status changes back to `playing`.

Pausing and resuming the camera feed does not affect the underlying `DataSource` playback or recording session. It only controls the delivery of camera frames to the controller. Use the `Playback API` available on the `DataSource` to control overall playback or recording for log data sources.

### Listen to Changes
The `GemCameraPlayerController` extends `ValueNotifier`, so you can listen to it for changes in playback status and update your UI accordingly. The `GemCameraPlayerValue` provides access to the data source, the current playback status, the current `Camera` (if available), and the data source listener used internally to receive camera frames.

### Lifecycle mMnagement
Dispose of the `GemCameraPlayerController` when it is no longer needed to free up resources. Disposing the controller stops the internal data source listener and releases any associated resources. The `GemCameraPlayer` widget does not automatically dispose of the controller, so manage its lifecycle appropriately in your application. Dispose of both the controller and the player widget when appropriate, as they can consume significant resources.

