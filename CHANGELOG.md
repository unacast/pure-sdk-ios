Any line containing "iOS11+", means only clients running iOS11 and greater will have access to the feature.

## 1.0.82
- Fix issue which was be trigered by a certain high priority beacon configuration.

## 1.0.81
- Ignores -1 battery report from iOS.
- Fixes incorrect Xcode project configuration resulting in logging code being included in release builds.

## 1.0.80
- Support for beacons that activate high priority tracking.
- Support for retrying iBeacon monitoring and ranging if iOS reports an error.
- All timestamps now have five decimal points.

- Tweak some calls to iOS to make iBeacon service much more reliable.
- Set `Pure.enableLogging = YES` before calling init to print logs to console.
- Reduce the amount of warning messages printed from Apple APIs.
- Do our best to mitigate buggy Apple KVO Swift implementations in Swift <= 4.2.
- Improve the likelyhood of timers triggering while we're operating in the background.
- Reduce the number of trips to the database while processing events, and reduce the frequency of saves.
- Wraps all database operations in background tasks to prevent iOS from terminating the app during the transaction.
- Perform clean up of internal files at regular intervals, vaccum database when opening.

- Fix crash which occured if we try to upload a missing file.
- Fix adidLimited being forced to true in config requests.
- Fix event deviceInfo containing invalid "timestamp" value.
- Fix issue that could cause us to schedule too many background config requests.
- Fix a bug which caused duplicate event uploads.
- Fix an issue which could cause us to miss a significant location event.

## 1.0.79
- Added "locale" and "publisherId" fields to device information.
- Allow customization of "publisherId" and "defaultConfigEndpoint" through framework Info.plist.
- Perform some more validity checks before attempting to upload an event blob.

## 1.0.78
- Fixes issue where our internal default storage could become unavailable if we are launched in the background after a reboot or force quit before the user unlocks their device.

## 1.0.77
- Changes encoding of "rssi" in Eddystone events to a number, previously it was a string.

## 1.0.76
- Fixes case where we could loose track of a serialized event blob saved to disk.
- Tweak uploading code so we're more resilient to force quits.
- Better rescheduling of failed uploads.

## 1.0.75
- Lower memory usage while deleting objects from local database.
- Slight network request optimizations.
- Fixes a few memory leaks.

## 1.0.74
- Fixes crash which could occur if an event is saved without its data blob.
- Background requests are now used to upload events.
- iOS11+ : Event uploads are immediately scheduled with a future start time.
- iOS11+ : Improve time between config fetch times by scheduling fetch when app enters background state.
- Improve time between config fetches in apps that are backgrounded frequently.
- When `onlySendOnWifi` = YES, instead of waiting for WiFi to upload, immediately schedule a WiFi only network request.
- Fix issue caused by handling of unreachable beacons.
- Asynchronous versions of `pureIdentifier` and `isTracking` have been introduced to ensure the SDK has a chance to initialize first. The old versions are deprecated and will only work after the SDK has initialized.
- Fix case where SDK could make config request before the SDK was initialized.
- Introduce custom serialization code to speed defaults store.
- On launch, this update performs some internal updates, listed below. These will not cause any delays in application startup.
    1. Moves SDK files out of `Documents`, and into our own folder in `Application Support`.
    2. Updates encoding method used in internal key value storage.
    3. Performs a file attributes update on internal files.

## 1.0.73
- Support for Carthage!
- Rework data-flow in serialization code to fix crash caused by unexpected mutations of data.
- Tentative fix for crash in database subsystem.
- We now properly delay iBeacon, Eddystone, and Visit events if they arrive before our first location event.

## 1.0.72
- Fixes crash related to `pur_isFluxloopRegion`.

## 1.0.71
- Fix issue in build system affecting customers using Enterprise Distribution.

## 1.0.70
- Report more hardware capabilities to server.

## 1.0.69
- Allow unreachable beacons into our processing pipeline.
- Allow unreachable beacons to cause live events.
- Filter out Visit events that arrive after the SDK has been stopped.
- Delete stored log messages after uploading them to our debug server.
- Fetch cloud config when user location authorization changes, or when `-[Pure startTracking]` turns on the SDK.
- Add a hard limit to how frequently the cloud configuration service is allowed to fetch config.
- Fixed an issue that could manifest when calling `createEvent` or `associateMetadata` immediately after initializing the SDK.
- Tentative fix for crash in `valueForDefault:`.

## 1.0.68
- Prevent the SDK from attatching itself as the UNNotificationCenter delegate unless debug mode is enabled.

## 1.0.67
- Fixes SDK reporting version number '1.0' to server.

## 1.0.66
- Fix crash caused by concurrent execution of `-[CLLocationManager locationServicesEnabled]`
- Implement internal timer with different timer API, with better background and battery performance.
- Improve the speed at which live iBeacons are reported.
- Dispatch to main thread before calling some methods on a location manager.
- Create our tracked beacons with our own prefix, as to not conflict with any beacons you may be tracking.
- Upload error logs to internal tracking endpoint.

## 1.0.65
- Increases reliability of iBeacon event delivery while switching between foreground, background, terminated states.
- Corrects Content-Type header sent with event payload.

## 1.0.64
- Fixes multithreading issue. 

## 1.0.63
- Improve battery efficiency.
- Adressed remaining memory leaks.
- Fixed issue that prevented clients from archiving while bitcode was enabled.
- Fix a bug that caused events with empty "event" payloads to be sent to the server.
- Fixed issue that could cause the config service to fetch more often than necessary.
- Fixed an issue that caused the "enableWhenInUse" config flag to have no effect.
- Fixed an issue that caused the GPS to be enabled on app start when it was not needed.
- Avoid sending events to server before we've fetched config for the first time, for privacy concerns.
- API: adidLimited now reports the correct value while sending in device configuration. (previously it was negated)
- API: connection.timestamp defaults to current date if the last changed timestamp is not available.
- API: remember connection change timestamps across restarts for more accurate information.
- API: if connection.cellType is not available, do not include instead of including empty string.
- API: changed device.powersave default value from "unknown" to false.
- API: visit.hacc, eddystone.rssi, beacon.major, and beacon.minor are now not included if they are invalid.
- API: fixed sending extra information in beacon.uuid, field is now correctly formatted UUID.

## 1.0.62
- Updated internal AFNetworking code to fix 2 data races.
- Audited and fixed CoreData implementation for data races.
- Added locking to prevent concurrent modification to shared dictionaries.
- Fixed a warning in CoreData on iOS 9.

## 1.0.61
- APIClient: Updated default value from "unknown" to "0001-01-01" for the departure date of a visit.
- APIClient: Updated default value from "unknown" to -1 for the battery level.
- APIClient: Fixed wrong version reported in HTTP calls.
- Bitcode enabled on required architecture slices. (armv7 + arm64)
- SDK reports correct version number when not integrated with Cocoapods.
- Public Interface: Added overload for custom event methods. New method allows user to force send events even if SDK is off. Event methods without the overload default to force = NO.
- Include the latest location in the connection event payload.
- Separate cloud config for when in use and always location tracking for better control of SDK.
- Added ClientID property to Pure.h

## Known Issues

#### Known Bitcode Issue - Fixed in .63
Archiving while bitcode is turned on results in the below error. Workaround : Upgrade to version 1.0.63, or head to project settings, search for "Bitcode" and temporarily disable the "Enable Bitcode" setting.

ld: bitcode bundle could not be generated because 'Pods/PureSDK/PureSDK.framework/PureSDK' was built without full bitcode. All frameworks and dylibs for bitcode must be generated from Xcode Archive or Install build file 'Pods/PureSDK/PureSDK.framework/PureSDK' for architecture armv7
