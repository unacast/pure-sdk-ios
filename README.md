## Pure SDK iOS

### Requirements
The iOS SDK is available for applications targeting the iOS 9.0 SDK and above.

### Dependencies
The iOS SDK has no dependencies.

### Installation
*You need a password to use this pod, and Unacast should have provided you with one.*

#### Adding to your project

First off, add the following lines to your app's `Podfile` :
```ruby
use_frameworks!
pod 'PureSDK', :podspec => 'http://puresdk.azurewebsites.net/cocoapods/versions/latest?key=PASSWORD'
```

Then, find your app's `Info.plist`, right click, select `Open As`, tap `Source Code`.  Paste the following :

```xml
<!-- All iOS versions -->
<key>NSLocationWhenInUseUsageDescription</key>
<string>We'll show you interesting things around you.</string>

<!-- iOS 10 and below only -->
<key>NSLocationAlwaysUsageDescription</key>
<string>We'll show you interesting things around you, and send you notifications when you come across something cool.</string>

<!-- iOS 11 and above only -->
<key>NSLocationAlwaysAndWhenInUsageDescription</key>
<string>We'll show you interesting things around you, and with the "always" option, we'll also send you notifications when you come across something cool.</string>

See appendix [1] for information about these keys.
These keys will be shown to the user when you request permissions. For the best results, change them to something that better suits your app.

### Usage
Start the SDK by calling the following method inside your App Delegate:

AppDelegate.m didFinishLaunchingWithOptions:
```objective-c
[PureSDK startWithLaunchOptions:launchOptions];
```
After the SDK is started, it remains idle until the appropriate permissions are granted. Our SDK will never trigger any permission prompts. The SDK listens for the following permission prompts :

1. Location Permission  : Allows the to SDK to track location. Supports `Always`, and  `WhenInUse`.
2. Motion Permission : Allows the SDK to optimize location tracking. Greatly increases location accuracy while also reducing battery usage.

It is up to you to ask the user for permission to access private data. Because of the design of the SDK, you don't have to wait until the user accepts permissions to start the SDK. One call and you're good to go. The SDK will automatically adapt to any and all permission changes.

To stop the SDK, call :
```objective-c
[PureSDK stopTracking];
```

### Configuration

All config variables used by the SDK are provided by an external service. This means it's possible to change the behaviour of the SDK without releasing a new version and configure
settings per-device using filters based on device type, iOS version, etc.

### Custom Data

The SDK provides methods to send additional `metadata` about the user or device in addition to `events`. The difference between `metadata` and `events`, is that `metadata` will replace any existing data with the same `type` while `events` will be added and never replaced even when using the same *name*

#### Event

To create an event, use :
```objectivec
[PureSDK createEvent:<#(nonnull NSString *)#> payload:<#(nonnull NSDictionary *)#> success:^{
    <#code#>
} failure:^(NSError * _Nullable error) {
    <#code#>
}];
```
The `type` describes what kind of event this is. Events are always added, not replaced.
Subsequent calls to `createEvent` with the same `type` will create multiple events in the cloud.

#### Metadata

```objective-c
[PureSDK associateMetadata:<#(nonnull NSString *)#> payload:<#(nonnull NSDictionary *)#> success:^{
    <#code#>
} failure:^(NSError * _Nullable error) {
    <#code#>
}];
```
The `type` has to be unique for each model you want to preserve.
Subsequent calls for `associateMetadata` with the same `type` will overwrite the data sent on previous calls.

## Data Collection
With the default configuration, the SDK will collect the following information :

Physical Location
Nearby iBeacons and Eddystone beacons
Network Connection Status

Sample Location Event :
```
{
    "vacc":17.2101072,
    "updated":"2018-04-26T01:41:23+00:00",
    "hacc":1414.0000256,
    "lon":-122.1871488,
    "lat":37.8745792,
    "type":"LOCATION",
    "altitude":69.7735168,
    "timestamp":"2018-04-26T01:41:24+00:00"
}
```

Sample iBeacon Event :
```
{
    "minor": 2,
    "rssi": 3,
    "major": 1,
    "type": "IBEACON",
    "timestamp": "2018-02-09T19:14:15-08:00",
    "uuid": "2F234454-CF6D-4A0F-ADF2-F4911BA9FFA6"
}
```

### Appendix

[1] How are the `Info.plist` strings used?

| Key | Description |
|--|--|
| NSLocationWhenInUse | Describes how your app will use location services in the foreground. |
| NSLocationAlways | Describes how your app will use location services, both in the foreground and background. The presented alert gives the user the option between "Always", and "Never". Will only be used if the device is running iOS 10 or earlier. |
| NSLocationAlwaysAndWhenIn | Describes how your app will use location services and explains what extra features you can provide if the always option is selected. The presented alert gives the user the option between "Only when in use", "Always", and "Never". Will only be used on iOS 11 or later. |

## What kind of data does the SDK collect
With the default configuration, the SDK will collect information about what WiFi the user is connected to, his or her current location, and any nearby iBeacons. All events come with information about the device, such as the current battery level.

The following is an example of a geolocation event. All of these data are sent with each event.

```json

{

"events" : {
	"device": {
		"activity": true,
		"adid": "1e90927e-8e19-1348-b7e0-f36732db49bc",
		"adidLimited": false,
		"appId": "com.pure.example",
		"appVersion": "1.1",
		"location": "always",
		"manufacturer": "apple",
		"mcc": 242,
		"mnc": 12,
		"model": "F8331",
		"os": "iPhone7,1",
		"osVersion": "11.2",
		"sdkVersion": "1.0",
		"timestamp": "2018-02-01T11:49:31+00:00",
		"trackingEnabled": true
	},

	"state" : {
		"activity": "stationary,automotive",
		"battery": 1.0,
		"charging": false,
		"foreground": true,
		"timestamp" : "2018-02-01T11:49:31+00:00"
	},

	"location" : {
		"altitude": 2,
		"bearing": 2.2,
		"lat": 12.3232322,
		"lon": 12.3232322,
		"speed": 4,
		"timestamp": "2018-02-01T11:49:31+00:00",
		"hacc": 2,
		"vacc": 4,
		"type": "LOCATION"
	},

	"event" : {
		"altitude": 2,
		"bearing": 2.2,
		"lat": 12.3232322,
		"lon": 12.3232322,
		"speed": 4,
		"timestamp": "2018-02-01T11:49:31+00:00",
		"hacc": 2,
		"vacc": 4,
		"type": "LOCATION"
	},

	"connection" : {
		"bssid": "Testing",
		"updated": "2018-02-01T11:49:31+00:00",
		"ssid": "testing",
		"type": "wifi"
	}
}
}

```



