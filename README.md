# react-native-secure-storage

## Fork explanation

This repository is a fork of [oyyq99999/react-native-secure-storage](https://github.com/oyyq99999/react-native-secure-storage).

Due to outdated developer tools and dependencies, it eventually became impossible to keep the dependency while upgrading our apps to the latest versions of React Native (specifically [v0.67](https://react-native-community.github.io/upgrade-helper/?from=0.66.4&to=0.67.0-rc.5)). We've introduced the required updates and created a [PR to the original repository](https://github.com/oyyq99999/react-native-secure-storage/pull/46). Unfortunately, we have not received any response as the repository is unmaintained. Since Shopify apps depend on this library and there is no easy way to migrate from it, we forked the library under Shopify organization and introduced the updates.

[react-native-keychain](https://github.com/oblador/react-native-keychain) would be a good candidate for replacement but the mechanism for saving and retrieving items on Android differs too much from `react-native-secure-storage` (as described [here](https://github.com/Shopify/react-native-packages/issues/1661#issue-1073117246)).
---
**NOTE**

We don't recommend new apps to use react-native-secure-storage since it's possible to use actively maintained react-native-keychain instead. This fork exists only to help apps that already depend on react-native-secure-storage and cannot easily migrate. 
---

## About

This package is based on [react-native-keychain](https://www.npmjs.com/package/react-native-keychain) and implemented a secure storage engine. It is compatiable with [redux-persist-sensitive-storage](https://www.npmjs.com/package/redux-persist-sensitive-storage)

## Getting started

`$ npm install react-native-secure-storage --save`

or

`$ yarn add react-native-secure-storage`

### Mostly automatic installation

`$ react-native link react-native-secure-storage`

### Manual installation


#### iOS

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules` ➜ `react-native-secure-storage` and add `RNSecureStorage.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libRNSecureStorage.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project (`Cmd+R`)<

#### Android

1. Open up `android/app/src/main/java/[...]/MainApplication.java`
  - Add `import li.yunqi.rnsecurestorage.RNSecureStoragePackage;` to the imports at the top of the file
  - Add `new RNSecureStoragePackage()` to the list returned by the `getPackages()` method
2. Append the following lines to `android/settings.gradle`:
  	```
  	include ':react-native-secure-storage'
  	project(':react-native-secure-storage').projectDir = new File(rootProject.projectDir, 	'../node_modules/react-native-secure-storage/android')
  	```
3. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
  	```
      implementation project(':react-native-secure-storage')
  	```


## Usage
```javascript
import SecureStorage, { ACCESS_CONTROL, ACCESSIBLE, AUTHENTICATION_TYPE } from 'react-native-secure-storage'

async() => {
  const config = {
    accessControl: ACCESS_CONTROL.BIOMETRY_ANY_OR_DEVICE_PASSCODE,
    accessible: ACCESSIBLE.WHEN_UNLOCKED,
    authenticationPrompt: 'auth with yourself',
    service: 'example',
    authenticateType: AUTHENTICATION_TYPE.BIOMETRICS,
  }
  const key = 'someKey'
  await SecureStorage.setItem(key, 'some value', config)
  const got = await SecureStorage.getItem(key, config)
  console.log(got)
}
```

### Methods

This library has now implemented `getItem`, `setItem`, `removeItem` and `getAllKeys` methods of `AsyncStorage` from [React Native](http://facebook.github.io/react-native/). It doesn't support callback and replaced the `callback` param with an `option` param.

In addition, this library has a `getSupportedBiometryType()` method which Returns one of `BIOMETRY_TYPE` indicating which biometry type the device supports, and a `canCheckAuthentication([{ authenticationType }])` method which checks whether the specified authenticationType is available.

### Options

| Key | Platform | Description | Default |
|---|---|---|---|
|**`accessControl`**|iOS only|This dictates how a keychain item may be used, see possible values in `SecureStorage.ACCESS_CONTROL`. |*None*|
|**`accessible`**|iOS only|This dictates when a keychain item is accessible, see possible values in `SecureStorage.ACCESSIBLE`. |*`SecureStorage.ACCESSIBLE.WHEN_UNLOCKED`*|
|**`accessGroup`**|iOS only|In which App Group to share the keychain. Requires additional setup with entitlements. |*None*|
|**`authenticationPrompt`**|iOS only|What to prompt the user when unlocking the keychain with biometry or device password. |`Authenticate to retrieve secret data`|
|**`authenticationType`**|iOS only|Policies specifying which forms of authentication are acceptable. |`SecureStorage.AUTHENTICATION_TYPE.DEVICE_PASSCODE_OR_BIOMETRICS`|
|**`service`**|All|Qualifier for the service. |*App bundle ID*|

#### `SecureStorage.ACCESS_CONTROL` enum

| Key | Description |
|-----|-------------|
|**`USER_PRESENCE`**|Constraint to access an item with either Touch ID or passcode.|
|**`BIOMETRY_ANY`**|Constraint to access an item with Touch ID for any enrolled fingers.|
|**`BIOMETRY_CURRENT_SET`**|Constraint to access an item with Touch ID for currently enrolled fingers.|
|**`DEVICE_PASSCODE`**|Constraint to access an item with a passcode.|
|**`APPLICATION_PASSWORD`**|Constraint to use an application-provided password for data encryption key generation.|
|**`BIOMETRY_ANY_OR_DEVICE_PASSCODE`**|Constraint to access an item with Touch ID for any enrolled fingers or passcode.|
|**`BIOMETRY_CURRENT_SET_OR_DEVICE_PASSCODE`**|Constraint to access an item with Touch ID for currently enrolled fingers or passcode.|

#### `SecureStorage.ACCESSIBLE` enum

| Key | Description |
|-----|-------------|
|**`WHEN_UNLOCKED`**|The data in the keychain item can be accessed only while the device is unlocked by the user.|
|**`AFTER_FIRST_UNLOCK`**|The data in the keychain item cannot be accessed after a restart until the device has been unlocked once by the user.|
|**`ALWAYS`**|The data in the keychain item can always be accessed regardless of whether the device is locked.|
|**`WHEN_PASSCODE_SET_THIS_DEVICE_ONLY`**|The data in the keychain can only be accessed when the device is unlocked. Only available if a passcode is set on the device. Items with this attribute never migrate to a new device.|
|**`WHEN_UNLOCKED_THIS_DEVICE_ONLY`**|The data in the keychain item can be accessed only while the device is unlocked by the user. Items with this attribute do not migrate to a new device.|
|**`AFTER_FIRST_UNLOCK_THIS_DEVICE_ONLY`**|The data in the keychain item cannot be accessed after a restart until the device has been unlocked once by the user. Items with this attribute never migrate to a new device.|
|**`ALWAYS_THIS_DEVICE_ONLY`**|The data in the keychain item can always be accessed regardless of whether the device is locked. Items with this attribute never migrate to a new device.|

#### `SecureStorage.AUTHENTICATION_TYPE` enum

| Key | Description |
|-----|-------------|
|**`DEVICE_PASSCODE_OR_BIOMETRICS`**|Device owner is going to be authenticated by biometry or device passcode.|
|**`BIOMETRICS`**|Device owner is going to be authenticated using a biometric method (Touch ID or Face ID).|

#### `SecureStorage.BIOMETRY_TYPE` enum

| Key | Description |
|-----|-------------|
|**`TOUCH_ID`**|Device supports authentication with Touch ID.|
|**`FACE_ID`**|Device supports authentication with Face ID.|
|**`FINGERPRINT`**|Device supports authentication with Android Fingerprint.|

  
