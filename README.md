# MobileMessaging SDK

[![Version](https://img.shields.io/cocoapods/v/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![License](https://img.shields.io/cocoapods/l/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![Platform](https://img.shields.io/cocoapods/p/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)

## Usage

To run the example project, clone the repo, and run `pod install` from the Example directory first.

#TODO: add some commercial text here

## Mobile Messaging Quick Start
1. Prepare your app on  Apple Developer website (https://developer.apple.com/account/ios/certificate/):
	1. Setup App ID for your application to support Push Notifications.
	2. Create, download and add new APNs Certificate to your Keychain.
2. Prepare your Infobip account (https://portal.infobip.com/push/):
	1. Create new application on Infobip Push portal.
	2. Upload your APNs Certificate as an *.p12 file (From your Keychain: Right-click on it, select "Export", and save it as a .p12 file.)
	3. Get your Infobip Application Code.
3. Create a new XCode Project.
4. Configure the new project to support Push Notifications:
	1. Click on "Capabilities", then turn on Push Notifications.
	2. Turn on Background Modes and check the Remote notifications checkbox.
5. Install MobileMessaging using Cocoa Pods. The podfile example:

	```
	source 'https://github.com/CocoaPods/Specs.git'
	platform :ios, '8.0'
	use_frameworks!
	pod 'MobileMessaging'
	```
	
<table>
	<tbody>
		<tr>
			<th align="center">Traditional</th>
			<th align="center">AppDelegate inheritance</th>
		</tr>
		<tr>
			<td>
```swift
// Swift
import IBMobileMessaging
```
			</td>
			<td>123</td>
		</tr>
	</tbody>
</table>

+---------------+---------------+--------------------+
| Fruit         | Price         | Advantages         |
+===============+===============+====================+
| Bananas       | $1.34         | - built-in wrapper |
|               |               | - bright color     |
+---------------+---------------+--------------------+
| Oranges       | $2.10         | - cures scurvy     |
|               |               | - tasty            |
+---------------+---------------+--------------------+
