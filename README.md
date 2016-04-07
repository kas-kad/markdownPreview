# MobileMessaging

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
    3. Upload your APNs Certificate as an *.p12 file (From your Keychain: Right-click on it, select "Export", and save it as a .p12 file.)
    2. Get your Infobip Application Code.
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
6. Perform code modification to the app delegate in order to receive push notifications:
  	1. Import the library:
        ```
        // Swift
        import IBMobileMessaging
        ```
        ```
        // Objective-C
        @import IBMobileMessaging;
        ```
  	2. Start MobileMessaging service using your Application Code as a parameter:
	```
	// Swift
	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
	    IBMobileMessaging.startWithApplicationCode("application_code")
	    ...
	}	
	```
	```
	// Objective-C
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		[IBMobileMessaging startWithApplicationCode:@"application_code"];
	...
	}
	```
  	3. Setup notification types that you want to use and register for remote notifications:
        ```
        // Swift
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
            IBMobileMessaging.startWithApplicationCode("application_code")

            let userNotificationTypes: UIUserNotificationType = [.Alert, .Badge, .Sound]
            let settings = UIUserNotificationSettings(forTypes: userNotificationTypes, categories: nil)
            application.registerUserNotificationSettings(settings)
            application.registerForRemoteNotifications()
            ...
        }
        ```
        ```
        // Objective-C
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            [IBMobileMessaging startWithApplicationCode:@"application_code"];

            UIUserNotificationType userNotificationTypes = (UIUserNotificationTypeAlert |
                                                          UIUserNotificationTypeBadge |
                                                          UIUserNotificationTypeSound);
            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:userNotificationTypes categories:nil];
            [application registerUserNotificationSettings:settings];
            [application registerForRemoteNotifications];
            ...
        }
        ```
  	4. Override method `application:didRegisterForRemoteNotificationsWithDeviceToken:` in order to inform Infobip about the new device registered:
        ```
        // Swift
        func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData) {
            IBMobileMessaging.didRegisterForRemoteNotificationsWithDeviceToken(deviceToken)
        }
        ```
        ```
        // Objective-C
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
            [IBMobileMessaging didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
        }
        ```
  	5. Override method `application:didReceiveRemoteNotification:fetchCompletionHandler:` in order to send notification delivery reports to Infobip:

        ```
        // Swift
        func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void) {
            IBMobileMessaging.didReceiveRemoteNotification(userInfo, fetchCompletionHandler: completionHandler)
        }
        ```
        
        ```
        // Objective-C
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
            [IBMobileMessaging didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
        }
        ```
