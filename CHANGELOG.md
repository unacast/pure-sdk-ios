Any line containing "iOS11+", means only clients running iOS11 and greater will have access to the feature.

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
