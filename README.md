## Pure SDK iOS

### Requirements
The iOS SDK is available for applications targeting the iOS 9.0 SDK and above.

### Dependencies
The iOS SDK has no dependencies.

### Installation

#### Adding to your project

1. Download the SDK from https://puresdk.azurewebsites.net/ (credentials are provided by Unacast):

2. Unzip the archive to ~/Documents/PureSDK.

3. Drag the PureSDK.framework file into the Frameworks group of Xcode's Project Navigator. When prompted, select "Copy items if needed" and continue.

4. Find your app's Info.plist, right click, select "Open As", tap "Source Code", and paste the following.
These strings will be shown to the user when you request permissions. For the best results, change them to something that better suits your app :

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>We'll show you interesting things around you.</string>

<!-- iOS 10 or earlier -->
<key>NSLocationAlwaysUsageDescription</key>
<string>We'll show you interesting things around you, and send you notifications when you come across something cool.</string>

<!-- iOS 11 -->
<key>NSLocationAlwaysAndWhenInUsageDescription</key>
<string>We'll show you interesting things around you, and with the "always" option, we'll also send you notifications when you come across something cool.</string>

<!-- Optional - Allows us to track much more accurately and with less battery usage! Best of both worlds :) -->
<key>NSMotionUsageDescription</key>
<string>Give you suggestions based on your activity.</string>
```

What do these keys mean?
- NSLocationWhenInUseUsageDescription : Describes how your app will use location services when the app is currently visible on the screen.
- NSLocationAlwaysUsageDescription : Describes how your app will use location services, both in the foreground and background. The presented dialogue will give the user the option of agreeing with "Always", or completely disable location services. Will only be used if the device is running iOS 10 or earlier
- NSLocationAlwaysAndWhenInUsageDescription : Describes how your app will use location services and explains what extra features you can provide if the always option is selected. The presented dialogue gives the user the option between "Only when in use", "Always", or completely disable location services. Will only be used on iOS 11 or later.
- NSMotionUsageDescription (optional) : Describes how your app uses motion tracking. If the user accepts this permission, we utilize the motion data to provide more accurate tracking with even less battery usage.

5. That's it!

*NOTE: A pod will be published imminently as an alternative option*

### Usage
Start the SDK by calling the following method :

```objective-c
[Pure startWithLaunchOptions:launchOptions];
```

The SDK will never trigger any permission prompts by itself, and automatically start collecting data once permissions have been accepted. It is up to you to ask the user for permission to access private data. You don't have to wait until the user accepts permissions to start the SDK.
We collect location data and use motion data for more accurate tracking. We've designed the iOS SDK to be super simple. One call and you're good to go. The SDK will automatically adapt to any and all permission changes.

### Configuration
All configurations used by the SDK are provided by an external endpoint. This makes it possible to change the configuration without releasing a new version of the application. It also makes it possible to use different configurations based on things like device type, iOS version, etc.

## What kinds of data does the SDK collect
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



