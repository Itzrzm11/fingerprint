# BiometricAuthentication
Use Apple FaceID or TouchID authentication in your app using BiometricAuthentication.
It's very simple and easy to use that handles Touch ID and Face ID authentication based on the device.

**Note:** - Face ID authentication requires user's persmission to be add in info.plist.
```swift
<key>NSFaceIDUsageDescription</key>
<string>This app requires Face ID permission to authenticate using Face recognition.</string>
```

### What's new in version 3.1
- Updated to Swift 5.0
- Implemented **Result** type as completion callback

### Version 2.2
- Set **AllowableReuseDuration** (in seconds) to auto authenticate when user has just unlocked the device with biometric.
- This is pretty useful when app comes to foreground or device is just unlocked by the user and you want to authenticate with biometrics.
- If you don't want to reuse the recently used authentication then simply skip this step.
```swift

// set this before calling authenticateWithBioMetrics method (optional)
BioMetricAuthenticator.shared.allowableReuseDuration = 60
```

### Version 2.1
- Check if **TouchID**  or **FaceID** authentication is available for iOS device.


![Alt text](https://raw.githubusercontent.com/rushisangani/BiometricAuthentication/master/Images/image1.png "Authenticate")
![Alt text](https://raw.githubusercontent.com/rushisangani/BiometricAuthentication/master/Images/image2.png "Fallback title")
![Alt text](https://raw.githubusercontent.com/rushisangani/BiometricAuthentication/master/Images/image3.png "Locked out")

## Features

- Works with Apple Face ID (iPhone X, Xs, XR, XsMax) and other Touch ID having devices.
- Predefined error handling when recognition fails.
- Automatic authentication with device passcode on multiple failed attempts.

## Requirements

- iOS 12.0+
- Xcode 10+
- Swift 3.0+

## Installation

### CocoaPods

```ruby
pod 'BiometricAuthentication'
```

### Carthage

```ruby
github "rushisangani/BiometricAuthentication"
```

## Usage

### Authenticate with biometric

```swift
BioMetricAuthenticator.authenticateWithBioMetrics(reason: "") { (result) in

    switch result {
    case .success( _):
        print("Authentication Successful")
    case .failure(let error):
        print("Authentication Failed")
    }
}
```
- When reason specified as empty - default will be used based on the device. Ex. for iPhone X - **"Confirm your face to authenticate."**,  For other devices - **"Confirm your fingerprint to authenticate."**

### Can Authenticate with biometric

- Alternatively you can check before authentication by following. This will check that if device supports Touch ID or Face ID authentication and your app can use that as of now.

```swift
if BioMetricAuthenticator.canAuthenticate() {

    BioMetricAuthenticator.authenticateWithBioMetrics(reason: "") { (result) in
        // check result -> success or failure
    }
}
```
### Check for Face ID
- Check if device supports face recognition or not.
```swift
if BioMetricAuthenticator.shared.faceIDAvailable() {
    // device supports face id recognition.
}
```
### Check for Touch ID
- Check if device supports touch id authentication or not.
```swift
if BioMetricAuthenticator.shared.touchIDAvailable() {
    // device supports touch id authentication
}
```

### Fallback Reason
- Fallback reason title will be shown when first authentication attempt is failed.
- You can give alternate authentication options like enter 'username & password' when user clicks on fallback button.
- Default reason is empty, which will hide fallback button.

```swift
BioMetricAuthenticator.authenticateWithBioMetrics(reason: "Biometric Authentication", fallbackTitle: "Enter Credentials") { (result) in

    switch result {
    case .success( _):
        // proceed further

    case .failure(let error):

        switch error {
        case .fallback:

            print("Authentication Failed")

            // show alternatives on fallback button clicked
            // for ex. - enter username/email and password

        default:
            break
        }
    }
}
```

### BiometryLockedout
- When biometry authentication is locked out after multiple failed attempts. You can unlock it by passcode authentication.
- Provide your own passcode authentication reason here, default will be used if not provided.

```swift
BioMetricAuthenticator.authenticateWithPasscode(reason: message) { (result) in
    switch result {
    case .success( _):
        // passcode authentication success
    case .failure(let error):
        print(error.message())
    }
}
```

### Error Handling
- There are various cases when biometric authentication can be failed.

1. **fallback**
    - Called when user clicks on provided fallback button.
2. **biometryNotEnrolled**
    - Called when no fingerprints or face is registered with the device.
    - You can show message to register a new face or fingerprint here.
    - Default message will be shown if not provided.
3. **canceledByUser**
    - Called when authentication canceled by user.
4. **canceledBySystem**
    - Called when authentication canceled by system when app goes into background or any other reason.
5. **passcodeNotSet**
    - Called when device passcode is not set and trying to use biometry authentication.
    - We can ask user to set device passcode here by opening Settings Application.
6. **failed**
    - Called when multiple failed attempts made by user.
    - You can show error message to user here.
    - Default message can be shown if not provided.
7. **biometryLockedout**
    - Called when more than 5 failed attempts made using biometric authentication. This will locked your biometry system.
    - You'll restrict user when this error is occured.
    - Aleternatively you can ask user to enter device passcode to unlock biometry.
8. **biometryNotAvailable**
    - Called when device does not support Face ID or Touch ID authentication.


