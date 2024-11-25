<!-- [![Swift Version](https://img.shields.io/badge/Swift-5.9-green)](https://www.swift.org) ![iOS Version](https://img.shields.io/badge/iOS-13-blue) -->

# Sanuk SDK Swift

SanukSDK is a powerful tool that enables you to integrate robust driving behavior analysis into your mobile app. By leveraging cutting-edge technology, our SDK provides accurate trip tracking, intelligent behavior detection, and personalized insights to enhance user experiences and promote safer driving.

#### Key Capabilities

* **Recording** – SDK automatically captures every trip taken by the user.
* **Scoring** – Analyzes driving behaviors and assigns a score based on various metrics.
* **Driving Events** – Provide various driving events after trip processing.
* **Collision Detection** - Notify to user and admin when collision is detected.


## Requirements

* Xcode 15.0
* Swift 5.9
* iOS 13.0


## Installation

Currently SanukSDKSwift only supports installation via Swift Package Manager. Once you have your Swift package set up, simply add the package at as a dependency in `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/Sanuk-AI/sanuk-sdk-swift.git", from: "0.0.1")
]
```


## Setup

To configure the SDK, you need to

#### Enable Background Modes

The `UIBackgroundModes` key in your app's `info.plist` file informs iOS about the specific background tasks your app intends to perform. This allows the system to grant your app temporary access to resources even when it's not in the foreground.

```
<key>UIBackgroundModes</key>
	<array>
		<string>fetch</string>
		<string>location</string>
		<string>processing</string>
	</array>
```

#### Add Capabilities

To allow your app to access sensor data from an SDK, you need to configure the app's `info.plist` file. This file specifies various settings for your app, including access permissions.

```
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
    <string>Your explanation.</string>
<key>NSLocationAlwaysUsageDescription</key>
    <string>Your explanation.</string>
<key>NSLocationWhenInUseUsageDescription</key>
    <string>Your explanation.</string>
<key>NSMotionUsageDescription</key>
    <string>Your explanation.</string>
```

#### Implement Delegate Method

Implement delegate methods of `SanukSDKDelegate` from any `UIViewController` to receive the information from SDK. 

```
extension ViewController: SanukSDKDelegate {

    func onStateChanged(state: SanukSDK.SanukSDKState) {
        print("onStateChanged : \(state)")
    }

    func onTripStarted() {
        print("onTripStarted")
    }

    //implement all other delegate methods

}
```

## Usage

This section of the readme will guide you through using the SDK into your project. First of all, you need to import the SDK by adding `import SanukSDK` to your file.

#### Starting the SDK

The `SanukSDKClient` class provides methods to interact with the SDK. To start the SDK, you will need to have a username and password or a token. Here's how to start the SDK:

```
class ViewController: UIViewController {
    
    private var sdk = SanukSDKClient.shared

    override func viewDidLoad() {
        super.viewDidLoad()
        
        sdk.start(
            login: "login@example.com",
            password: "password",
            delegate: self
        )
    }

}
```

Once the SDK finished starting, it would call the `onStateChanged` function from the `SanukSDKDelegate`.

```
extension ViewController: SanukSDKDelegate {
    
    func onStateChanged(state: SanukSDK.SanukSDKState) {
        print("onStateChanged : \(state)")
    }

}
```

You can also call the `isStarted` to check if SDK started.

```
if sdk.isStarted {
    print("SDK is started")
}
```

To stop the SDK, just simply call the `stop` function.

```
sdk.stop()
```

#### Retrieving the Trips

Once a trip is completed, you can get the trip data by calling the `getTrips` function. The `SdkTripModel` contains various trip-related metrics such as distance traveled, duration, and a calculated trip score.

```
sdk.getTrips() { trips in
    self.displayTrip(trips)
}
```

#### Get Driver's Score

Use the `getDriverscores` function to fetch driver score data. The `OverallScoreModel` contains driver's overall driving score and event scores such as speeding, phoneUse, breaking, acceleration and turning. 

```
sdk.getDriverscores() { score in
    let overall = score?.totalAverage.overall
    let speeding = score?.totalAverage.speeding
    let phoneUse = score?.totalAverage.phoneUse
    let breaking = score?.totalAverage.breaking
    let turning = score?.totalAverage.turning
    let acceleration = score?.totalAverage.acceleration
}
```
