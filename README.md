# Pure-SDK for iOS

## Requirements
The iOS SDK is available for applications targeting iOS 9.0 and above.

## Installation
*You need a key to use this pod, and Unacast should have provided you with one.*

### Cocoapods

First off, find your key and add the following lines to your app's `Podfile` :
```ruby
use_frameworks!
pod 'PureSDK', :podspec => 'http://puresdk.azurewebsites.net/cocoapods/sdk/versions/latest?key=INSERT_KEY_HERE'
```

### Dynamic Framework

Download the latest version of the pod from :

`https://puresdk.azurewebsites.net/cocoapods/sdk/versions/1.0.59.zip?key=INSERT_KEY_HERE`

1. Open your project in Xcode.
2. Drag and drop `PureSDK.framework` into your project. Make sure the `Copy files` box is checked. Click ok.
3. Select the target you wish to integrate the SDK into.
4. Find the "Linked Frameworks and Libraries" section, and delete the `PureSDK.framework` entry.
5. Find the `Embedded Binaries` section, and add the `PureSDK.framework` that you just included into your project.
6. Navigate to `Build Phases`, click the small `+` in the left area of the window, and click `New Run Script Phase`.
7. Paste the following line into the phase that was just added :
`bash "${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}/PureSDK.framework/strip-frameworks.sh"`

That's it! These instructions were last tested on Xcode 9.4. For comparison, your `General` tab should look like this :

![finished-settings](https://github.com/unacast/pure-sdk-ios/blob/master/dynamic_framework_integration_result.png)

## Permissions

Some of the SDK's data collection source require extra keys in `Info.plist`, and some require the user to accept a pop up. We will never trigger the permission popups from our code. It is your responsibility to present the permission prompt alert at an appropriate time.

### Location

Here are the `Info.plist` keys that Apple requires for location tracking :

- `NSLocationWhenInUseUsageDescription` : Describes how your app will use location services in the foreground. You must include this key if you wish to collect location events.

- `NSLocationAlwaysAndWhenInUseUsageDescription` : Describes how your app will use location services and explains what extra features you can provide if the always option is selected. The presented alert gives the user the option between "Only when in use", "Always", and "Never". Only used on iOS 11 or later.

- `NSLocationAlwaysUsageDescription` : Describes how your app will use location services, both in the foreground and background. The presented alert gives the user the option between "Always", and "Never". If your deployment target is at iOS 11 or higher, then you don't need this key even if you wish to collect location events.

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

### Bluetooth

Our bluetooth code comes bundled as an optional framework, since any app that uses bluetooth on iOS must explicity state its why it does so. If your app already has a use case for accessing bluetooth outside of tracking purposes, then this component can be included to automatically collect `Eddystone` events.

First, double check that the `NSBluetoothPeripheralUsageDescription` is set in your application's `Info.plist`. Failure to include this key will cause your app to be rejected on App Store Connect upload.

#### Coocoapods

To install the `PureSDKBluetooth` component, your podfile should contain this :
```ruby
use_frameworks!
pod 'PureSDK', :podspec => 'https://puresdk.azurewebsites.net/cocoapods/sdk/versions/latest?key=INSERT_KEY_HERE'
pod 'PureSDKBluetooth', :podspec => 'https://puresdk.azurewebsites.net/cocoapods/bluetooth/versions/latest?key=INSERT_KEY_HERE'
```

#### Dynamic Framework

First, download `PureSDKBluetooth.framework` at `https://puresdk.azurewebsites.net/cocoapods/versions/bluetooth/latest?key=INSERT_KEY_HERE`.
Then, follow instructions 1-5 as listed in  `Installation > Dynamic Framework`, except use `PureSDKBluetooth.framework` instead of `PureSDK.framework`.

## Usage

Two method calls are required to get the SDK to start tracking :

First in, `AppDelegate.m didFinishLaunchingWithOptions:`
```objective-c
[Pure initializeWithLaunchOptions:launchOptions];
```
Then, somewhere of your choosing (can be placed right after the above call if desired) : 
```objective-c
[Pure startTracking];
```

At any time, event collection can be stopped by calling :
```objective-c
[Pure stopTracking];
```

## Configuration

All config variables used by the SDK are provided by an external service. This means it's possible to change the behaviour of the SDK without releasing a new version and configure
settings per-device using filters based on device type, iOS version, etc.

## Custom Data

The SDK provides methods to send additional `metadata` about the user or device in addition to `events`. The difference between `metadata` and `events`, is that `metadata` will replace any existing data with the same `type` while `events` will be added and never replaced even when using the same *name*

### Event

To create an event, use :
```objective-c
[PureSDK createEventWithType:<#(nonnull NSString *)#> payload:<#(nonnull NSDictionary *)#> success:^{
    <#code#>
} failure:^(NSError * _Nullable error) {
    <#code#>
}];
```

The `type` describes what kind of event this is. Events are always added, not replaced.
Subsequent calls to `createEventWithType` with equal `type` values will create multiple events in the cloud.

### Metadata

```objective-c
[PureSDK associateMetadataWithType:<#(nonnull NSString *)#> payload:<#(nonnull NSDictionary *)#> success:^{
    <#code#>
} failure:^(NSError * _Nullable error) {
    <#code#>
}];
```

The `type` has to be unique for each model you want to preserve.
Subsequent calls to `associateMetadataWithType` with equal `type` value will overwrite the data sent on previous calls.

## Event Types

Here is an overview of what kinds of data we collect and upload.

1. Location
2. iBeacons
3. Network Connection
4. Eddystone UID
5. Eddystone URL

Sample Location Event :
```json
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

```json
{
    "minor": 2,
    "rssi": 3,
    "major": 1,
    "type": "IBEACON",
    "timestamp": "2018-02-09T19:14:15-08:00",
    "uuid": "2F234454-CF6D-4A0F-ADF2-F4911BA9FFA6"
}
```
