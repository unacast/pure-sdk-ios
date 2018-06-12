## Pure SDK iOS

### Requirements
The iOS SDK is available for applications targeting the iOS 9.0 SDK and above.

### Dependencies
The iOS SDK has no dependencies.

### Installation
*You need a key to use this pod, and Unacast should have provided you with one.*

#### Cocoapods

First off find your key and add the following lines to your app's `Podfile` :
```ruby
use_frameworks!
pod 'PureSDK', :podspec => 'http://puresdk.azurewebsites.net/cocoapods/versions/latest?key=INSERT_KEY_HERE'
```

#### Dynamic Framework

Download the latest version of the pod from :

https://puresdk.azurewebsites.net/cocoapods/versions/1.0.46.zip?key=INSERT_KEY_HERE

1. Open your project in Xcode.
2. Drag and drop PureSDK.framework into your project. Make sure the `Copy files` box is checked.
3. Select the target you wish to integrate the SDK into.
4. Find the "Linked Frameworks and Libraries" section, and delete the `PureSDK.framework` entry.
4. Find the "Embedded Binaries" section, and add the `PureSDK.framework` that you just included into your project.
(This was tested on Xcode 9.4)

##### Location Permissions

Some of the SDK's data collection source require extra keys in `Info.plist`, and some require the user to accept a pop up. We will never trigger the permission popups from our code. It is your responsibility to present the permission prompt alert at an appropriate time.

- NSLocationWhenInUseUsageDescription : Describes how your app will use location services in the foreground. You must include this key if you wish to collect location events.
- NSLocationAlwaysAndWhenInUseUsageDescription : Describes how your app will use location services and explains what extra features you can provide if the always option is selected. The presented alert gives the user the option between "Only when in use", "Always", and "Never". Only used on iOS 11 or later.
- NSLocationAlwaysUsageDescription | Describes how your app will use location services, both in the foreground and background. The presented alert gives the user the option between "Always", and "Never". If your deployment target is at iOS 11 or higher, then you don't need this key, even if you wish to collect location events

As a recap, if your application is targeting iOS 11 or higher and you wish to track location events, then only the `WhenInUseUsage` and `AlwaysAndWhenInUseUsage` plist keys are required.
If your application is targeting iOS 10 or below, then you need to include all keys for iOS 11 in addition to the `AlwaysUsage` key.

Find your app's `Info.plist`, right click, select `Open As`, tap `Source Code` and paste the keys you will be using. Remember, these keys will be shown to your users.

```xml
<!-- All iOS versions -->
<key>NSLocationWhenInUseUsageDescription</key>
<string>We'll show you interesting things around you.</string>

<!-- iOS 10 and below only -->
<key>NSLocationAlwaysUsageDescription</key>
<string>We'll show you interesting things around you.</string>

<!-- iOS 11 and above only -->
<key>NSLocationAlwaysAndWhenInUsageDescription</key>
<string>We'll show you interesting things around you, and with the "always" option, we'll also send you notifications when you come across something cool.</string>
```

### Usage
Start the SDK by calling the following method inside your App Delegate:

AppDelegate.m didFinishLaunchingWithOptions:
```objective-c
[PureSDK startWithLaunchOptions:launchOptions];
```

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
```objective-c
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

Location - Enabled only when the required location keys are included.
Nearby iBeacons beacons - iBeacons use the same permissions as location.
Network Connection Status - Always collected.

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
