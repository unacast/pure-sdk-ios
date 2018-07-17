## 1.0.61

- APIClient: Updated default value from "unknown" to "0001-01-01" for the departure date of a visit.
- APIClient: Updated default value from "unknown" to -1 for the batter level.
- APIClient: Fixed wrong version reported in HTTP calls.
- Bitcode enabled on required architecture slices. (armv7 + arm64)
- SDK reports correct version number when not integrated with Cocoapods.
- Public Interface: Added overload for custom event methods. New method allows user to force send events even if SDK is off. Event methods without the overload default to force = NO.
- Include the latest location in the connection event payload.
- Seperate cloud config for when in use and always location tracking for better control of SDK.
- Added ClientID property to Pure.h

#### Known Bitcode Issue
Archiving when integrating through Cocoapods while bitcode is turned on results in the below error. Workaround : Head to project settings, search for "Bitcode" and temporarily disable the "Enable Bitcode" setting. We will provide an update with a fix shortly. This issue does not affect regular compilation, as it only shows up while archiving.

ld: bitcode bundle could not be generated because 'Pods/PureSDK/PureSDK.framework/PureSDK' was built without full bitcode. All frameworks and dylibs for bitcode must be generated from Xcode Archive or Install build file 'Pods/PureSDK/PureSDK.framework/PureSDK' for architecture armv7