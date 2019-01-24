# Mobile Messaging SDK for iOS

[![Version](https://img.shields.io/cocoapods/v/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![License](https://img.shields.io/cocoapods/l/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![Platform](https://img.shields.io/cocoapods/p/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)

Mobile Messaging SDK is designed and developed to easily enable push notification channel in your mobile application. In almost no time of implementation you get push notification in your application and access to the features of <a href="https://www.infobip.com/en/products/mobile-app-messaging" target="_blank">Infobip Mobile Apps Messaging</a>. The document describes library integration steps. Additional information can be found in our <a href="https://github.com/infobip/mobile-messaging-sdk-ios/wiki" target="_blank">Wiki</a>.

## Requirements
- Xcode 10
- Swift 4.2
- iOS 9.0+

<!-- ## Usage -->
## Quick start guide
This guide is designed to get you up and running with Mobile Messaging SDK integrated into your iOS application.

1. Make sure to [setup application at Infobip portal](https://dev.infobip.com/push-messaging), if you haven't already.
2. Configure your project to support Push Notifications:
	1. Click on "Capabilities", then turn on Push Notifications. Entitlements file should be automatically created by XCode with set `aps-environment` value.
	2. Turn on Background Modes and check the Remote notifications checkbox.
3. Installation

	##### CocoaPods
	To integrate MobileMessaging into your Xcode project using [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#getting-started), specify it in your `Podfile`:

	```ruby
	source 'https://github.com/CocoaPods/Specs.git'
	platform :ios, '9.0'
	use_frameworks!
	pod 'MobileMessaging'
	```

	> #### Notice 
	> CocoaLumberjack logging used by default, in order to use other logging or switch it off follow [this guide](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/How-to-install-the-SDK-without-CocoaLumberjack%3F).

	##### Carthage
	If you use [Carthage](https://github.com/Carthage/Carthage/#readme) to manage your dependencies, just add MobileMessaging to your `Cartfile`:

	```
	github "infobip/mobile-messaging-sdk-ios"
	```

	If you use Carthage to build your dependencies, make sure you have added `MobileMessaging.framework` to the "Linked Frameworks and Libraries" section of your target, and have included them in your Carthage framework copying build phase (as described in [Carthage documentation](https://github.com/Carthage/Carthage/blob/master/README.md#if-youre-building-for-ios-tvos-or-watchos)).
	If your application target does not contain Swift code at all, you should also set the `EMBEDDED_CONTENT_CONTAINS_SWIFT` build setting to “Yes”.

4. Perform [code modification to the app delegate](#app-delegate-changes) in order to receive push notifications.

5. At this step you are all set for receiving regular push notifications. There are several advanced features that you may find really useful for your product, though:
	- [Rich Notifications and better delivery reporting(available with iOS 10)](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/Notification-Service-Extension-for-Rich-Notifications-and-better-delivery-reporting-on-iOS-10)
	- [Geofencing Service](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/Geofencing-service)

### App Delegate Changes

The simplest approach to integrate Mobile Messaging SDK with an existing app is by adding the SDK calls into your app delegate:

1. Import the library:

	```swift
	// Swift
	import MobileMessaging
	```

	```objective-c
	// Objective-C
	@import MobileMessaging;
	```
2. Start MobileMessaging service using your Infobip Application Code, obtained in step 2, and preferable notification type as parameters:

	```swift
	// Swift
	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        MobileMessaging.withApplicationCode(<#your application code#>, notificationType: <#for example UserNotificationType(options: [.alert, .sound])#>)?.start()
		...
	}	
	```

	```objective-c
	// Objective-C
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        UserNotificationType *userNotificationType = [[UserNotificationType alloc] initWithOptions:<#for example @[UserNotificationType.alert, UserNotificationType.sound]#>;
        [[MobileMessaging withApplicationCode: <#your application code#> notificationType: userNotificationType] start:nil];
		...
	}
	```

	Please note that it is not very secure to keep your API key (Application Code is an API key in fact) hardcoded so if the security is a crucial aspect, consider obfuscating the Application Code string (we can recommend [UAObfuscatedString](https://github.com/UrbanApps/UAObfuscatedString) for string obfuscation).

4. Override method `application:didRegisterForRemoteNotificationsWithDeviceToken:` in order to inform Infobip about the new device registered:

	```swift
	// Swift
	func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
		MobileMessaging.didRegisterForRemoteNotificationsWithDeviceToken(deviceToken)
	}
	```

	```objective-c
	// Objective-C
	- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
		[MobileMessaging didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
	}
	```
5. Override method `application:didReceiveRemoteNotification:fetchCompletionHandler:` in order to send notification delivery reports to Infobip:

	```swift
	// Swift
	func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void) {
		MobileMessaging.didReceiveRemoteNotification(userInfo, fetchCompletionHandler: completionHandler)
	}
	```

	```objective-c
	// Objective-C
	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
		[MobileMessaging didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
	}
	```
6. **Skip this step if your apps minimum deployment target is iOS 10 or later.** Override method `application:didReceiveLocalNotification`(for Objective-C) or `application:didReceive:`(for Swift) in order the MobileMessaging SDK to be able to handle incoming local notifications internally:

	```swift
	// Swift
	func application(_ application: UIApplication, didReceive notification: UILocalNotification) {
		MobileMessaging.didReceiveLocalNotification(notification)
	}
	```

	```objective-c
	// Objective-C
    -(void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        [MobileMessaging didReceiveLocalNotification:notification];
    }
	```

<br>
<center>**NEXT STEPS: [User profile](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/User-profile)**</center>
<br>

<br>

| If you have any questions or suggestions, feel free to send an email to support@infobip.com or create an <a href="https://github.com/infobip/mobile-messaging-sdk-ios/issues" target="_blank">issue</a>. |
|---|
