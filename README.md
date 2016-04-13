# MobileMessaging SDK

[![Version](https://img.shields.io/cocoapods/v/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![License](https://img.shields.io/cocoapods/l/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)
[![Platform](https://img.shields.io/cocoapods/p/MobileMessaging.svg?style=flat)](http://cocoapods.org/pods/MobileMessaging)

## General
Infobip Push library is designed and  developed to easily enable push notification channel in your mobile application. In almost no time of implementation you start sending push notification and use all feature of Infobip IP Messaging Platform. 
The document describes library integration steps.

## Usage
To run the example project, clone the repo, and run `pod install` from the Example directory first.

## Mobile Messaging Quick Start
### Preparing your application
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

	```ruby
	source 'https://github.com/CocoaPods/Specs.git'
	platform :ios, '8.0'
	use_frameworks!
	pod 'MobileMessaging'
	```
6. Perform code modification to the app delegate in order to receive push notifications. There are two ways to do this: [App Delegate Composition](#app-delegate-composition) or [App Delegate Inheritance](#app-delegate-inheritance)

### App Delegate Composition

1. Import the library:

	```swift
	// Swift
	import MobileMessaging
	```

	```objective-c
	// Objective-C
	@import MobileMessaging;
	```
2. Start MobileMessaging service using your Application Code as a parameter:

	```swift
	// Swift
	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
		MobileMessaging.startWithApplicationCode("your_application_code")
		...
	}	
	```

	```objective-c
	// Objective-C
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		[MobileMessaging startWithApplicationCode:@"your_application_code"];
		...
	}
	```
3. Setup notification types that you want to use and register for remote notifications:

	```swift
	// Swift
	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
		MobileMessaging.startWithApplicationCode("your_application_code")

		let userNotificationTypes: UIUserNotificationType = [.Alert, .Badge, .Sound]
		let settings = UIUserNotificationSettings(forTypes: userNotificationTypes, categories: nil)
		application.registerUserNotificationSettings(settings)
		application.registerForRemoteNotifications()
		...
	}
	```

	```objective-c
	// Objective-C
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		[MobileMessaging startWithApplicationCode:@"your_application_code"];

		UIUserNotificationType userNotificationTypes = (UIUserNotificationTypeAlert | UIUserNotificationTypeBadge | UIUserNotificationTypeSound);
		UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:userNotificationTypes categories:nil];
		[application registerUserNotificationSettings:settings];
		[application registerForRemoteNotifications];
		...
	}
	```
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

### App Delegate Inheritance

1. Import the library, into your `AppDelegate` declaration file:

	```swift
	// Swift
	import MobileMessaging
	```

	```objective-c
	// Objective-C
	@import MobileMessaging;
	```
2. Inherit your `AppDelegate` from `MobileMessagingAppDelegate` or `MobileMessagingAppDelegateObjc` depending on your project's language:

	```swift
	// Swift
	class AppDelegate: MobileMessagingAppDelegate {
		...
	}
	```

	```objective-c
	// Objective-C
	@interface AppDelegate : MobileMessagingAppDelegateObjc
	```
3. Override `applicationCode` and `userNotificationType` variables in your `AppDelegate` providing appropriate values:

	```swift
	// Swift
	override var applicationCode: String {
		return "your_application_code"
	}
	override var userNotificationType: UIUserNotificationType {
		return [.Alert, .Sound]
	}
	```

	```objective-c
	// Objective-C
	-(NSString *)applicationCode {
		return @"your_application_code";
	}
	-(UIUserNotificationType)userNotificationType {
		return UIUserNotificationTypeAlert | UIUserNotificationTypeSound;
	}
	```
4. If you have any of following application callbacks implemented in your AppDelegate:

	* `application(:didFinishLaunchingWithOptions:)`
	* `application(:didRegisterForRemoteNotificationsWithDeviceToken:)`
	* `application(:didReceiveRemoteNotification:fetchCompletionHandler:)`

	, rename it to corresponding:

	* `mm_application(:didFinishLaunchingWithOptions:)`
	* `mm_application(:didRegisterForRemoteNotificationsWithDeviceToken:)`
	* `mm_application(:didReceiveRemoteNotification:fetchCompletionHandler:)`
