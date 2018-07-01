## 1.0.61

- APIClient: Updated default value from "unknown" to  "0001-01-01" for the departure date of a visit.
- APIClient: Updated default value from "unknown" to -1 for the batter level.
- Bitcode enabled on required architecture slices. (armv7 + arm64)
- SDK reports correct version number when not integrated with Cocoapods.
- Public Interface: Added overload for custom event methods. New method allows user to force send events even if SDK is off. Event methods without the overload default to force = NO.
- Include the latest location in the connection event payload.
