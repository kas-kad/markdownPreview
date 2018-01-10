- [Prerequisites](#prerequisites)
- [Built-in Chat UI](#built-in-chat-ui)
  * [Customizing chat view](#customizing-chat-view)
  * [Using actionable chat messages](#using-actionable-chat-messages)
    + [Registering categories for chat](#registering-categories-for-chat)
    + [Sending messages with categories](#sending-messages-with-categories)
    + [Handling message actions](#handling-message-actions)
- [Mobile Chat iOS APIs](#mobile-chat-ios-apis)
  * [Sending messages](#sending-messages)
  * [Displaying messages](#displaying-messages)
  * [Changing user profile](#changing-user-profile)
  * [Handling notification taps](#handling-notification-taps)
  * [Message storage](#message-storage)
    + [Default message storage](#default-message-storage)
    + [Custom message storage](#custom-message-storage)

# Prerequisites

In order to start using Chat functionality the following requirements must apply:
- you must have a Push Application Code (a corresponding Push Application with a relevant APNS Certificate bound)
- your iOS application must suppport Push capabilities
To meet the requirements, please follow this [guide](https://github.com/infobip/mobile-messaging-sdk-ios#quick-start-guide).
- MobileChat SDK must be added as a dependency into your iOS app and started as a part of MobileMessaging SDK:
1. Declare a CocoaPods dependency by adding the following line in you `Podfile`:

```
pod 'MobileMessaging/MobileChat'
```
2. Start the Moblie Chat component by adding `withMobileChatDefaultStorage()` to the MobileMessaging initialization chain:

```swift
MobileMessaging
	.withApplicationCode(<# your applicatio code #>, notificationType: <# your notifications types preferences #>)?
	.withMobileChatDefaultStorage() // this line prepares mobile chat service to start
	.start()
```

# Built-in Chat UI

You can save a lot of time by leveraging predefined Chat UI components. Key component to use is either ChatVC or ChatNavigationVC. We support two ways of using these UI components:
- via interface builder: by setting the appropriate class name ("ChatVC" or "ChatNavigationVC") as `Custom Class` for your view controller object.
- programmatically: by presenting the appropriate chat view controller instance, for example:
```swift
if let chatVc = MobileMessaging.mobileChat?.chatViewController {
	present(chatVc, animated: true)
}
```
or
```swift
if let chatVc = MobileMessaging.mobileChat?.chatNavigationViewController {
	present(chatVc, animated: true)
}
```

It will immediately open chat view activity on top of current activity:

<img src="chat_view.png" alt="Chat View" style="width: 300px;"/>

## Customizing chat view

You can define your own custom appearance for chat view by accessing a chat settings object: `MobileMessaging.mobileChat.settings`. The following settings are available for customizations:
* `title` (String) - title for navigation bar when using ChatNavigationVC
* `tintColor` (UIColor) - main tint color for actionable controls such as Send button
* `navBarItemsTintColor` (UIColor) - tint color of navigation bar items when using ChatNavigationVC
* `navBarColor` (UIColor) - color of navigation bar when using ChatNavigationVC
* `navBarTitleColor` (UIColor) - color of title of navigation bar when using ChatNavigationVC

## Using actionable chat messages

In order to be able to use messages with action buttons, you need to register custom category which define the type of notification that your app supports.

### Registering categories for chat

You register [custom notification categories](https://github.com/infobip/mobile-messaging-sdk-ios/wiki/Interactive-Notifications#custom-categories) with associated actions for incoming chat messages:

```swift
let actionTry = NotificationAction(identifier: "action_try_for_free", title: "Try for free", options: [.chatCompatible])
let categoryOfferMessage = NotificationCategory(identifier: "category_offer", actions: [actionTry!], options: nil, intentIdentifiers: nil)
MobileMessaging
	.withApplicationCode(<# your applicatio code #>, notificationType: <# your notifications types preferences #>)?
	.withMobileChatDefaultStorage()
	.withInteractiveNotificationCategories([categoryOfferMessage!]) // this line registers catrgories for actionable notifications
	.start()
```

Message that contains any of categories will be rendered into message with buttons in chat view with maximum of 2 buttons. Below is the example for custom category to offer your product to a user:

<img src="message_with_action.png" alt="Chat View" style="width: 300px;"/>

### Sending messages with categories

You can then send messages with categories using [Chat HTTP API](../api/api.md).


### Handling message actions

In order to handle button taps for chat messages, you should create a class that implements `NotificationActionHandling` protocol and set this class as `notificationActionHandler`:

```swift
class CustomActionHandler : NotificationActionHandling {
	func handle(action: NotificationAction, forMessage message: MTMessage, withCompletionHandler completionHandler: @escaping () -> Void) {
		switch action.identifier {
			case "action_try_for_free":
				// perform some custom actions to let user try the offer
				completionHandler()
			default:
				// do nothing
				completionHandler()
			}
	}
}

MobileMessaging.notificationActionHandler = CustomActionHandler()
```

And don't forget to call `completionHandler()` within your implementation of `handle(action: forMessage: withCompletionHandler)`after the action handling is finished.

# Mobile Chat iOS APIs

Mobile Chat library provides set of APIs to send and receive messages and manage user profile.

## Sending messages

In order to send a chat message, use the following asynchronous method:

```swift
MobileMessaging.mobileChat?.send(text: "Hello, World!", completion: {
	chatMessage, error in
	// handle result if needed
})
```

you can also add custom data to a message (`customPayload` map):

```swift
MobileMessaging.mobileChat?.send(text: "Hello, World!", customPayload: ["key": "value"], completion: {
	chatMessage, error in
	// handle result if needed
})
```

## Displaying messages

In order to display messages in your custom UITableView/UICollectionView/other component, you can use `ChatMessagesController` which is buit on top of `NSFetchedResultsController`. All you need to do is to create a delegate class conforming to `ChatMessagesControllerDelegate` protocol. For example, the simplest implementation for UITableViewController would be:

```swift
class MyChatViewController: UITableViewController, ChatMessagesControllerDelegate {

	static let reuseIdentifierToDoCell = "messageCell"
	var chatMessagesController: ChatMessagesController!

	override func viewDidLoad() {
		super.viewDidLoad()
		// chatMessagesController setup
		chatMessagesController = MobileMessaging.mobileChat?.chatMessagesController
		chatMessagesController.delegate = self
		chatMessagesController.performFetch()
	}

//MARK: - ChatMessagesControllerDelegate implementation
	func controllerWillChangeContent(_ controller: ChatMessagesController) { }
	
	func controller(_ controller: ChatMessagesController, didChange message: ChatMessage, at indexPath: IndexPath?, for type: ChatMessagesChangeType, newIndexPath: IndexPath?) {
		switch (type) {
		case .insert:
			if let indexPath = newIndexPath {
				self.tableView.insertRows(at: [indexPath], with: .none)
				self.tableView.scrollToRow(at: indexPath, at: .bottom, animated: true)
			}
		case .delete:
			if let indexPath = indexPath {
				self.tableView.deleteRows(at: [indexPath], with: .automatic)
			}
		case .update:
			if let indexPath = indexPath {
				if let cell = self.tableView.cellForRow(at: indexPath) {
					configureCell(cell, atIndexPath: indexPath)
				}
			}
		case .move:
			if let indexPath = indexPath {
				self.tableView.deleteRows(at: [indexPath], with: .automatic)
			}
			
			if let newIndexPath = newIndexPath {
				self.tableView.insertRows(at: [newIndexPath], with: .automatic)
			}
		}
	}
	
	func controllerDidChangeContent(_ controller: ChatMessagesController) { }

//MARK: - UITableViewDataSource
	override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
		return chatMessagesController.fetchedMessagesCount
	}
	
	override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
		let cell = tableView.dequeueReusableCell(withIdentifier: ViewController.reuseIdentifierToDoCell, for: indexPath)
		configureCell(cell, atIndexPath: indexPath)
		return cell
	}

//MARK: - Helpers
	private func configureCell(_ cell: UITableViewCell, atIndexPath indexPath: IndexPath) {
		guard let chatMessage = chatMessagesController.chatMessage(at: indexPath) else {
			return
		}
		if chatMessage.isYours {
			cell.textLabel?.textAlignment = .right
		} else {
			cell.textLabel?.textAlignment = .left
		}
		cell.textLabel?.attributedText = NSAttributedString.makeChatMessageText(with: chatMessage)
	}
}
```

## Changing user profile

Each chat message has an author, and each author has following properties:
- id
- first name
- last name
- middle name
- nickname
- email
- gsm
- custom data

You are able to get details about the author of each incoming message by getting `ChatMessage.author` property. In order for other users to receive your details along with your messages, you should fill your user profile:

```swift
if let me = ChatParticipant.current {
	me.firstName = "Boris" // here you set user attributes
	me.nickName = "Blade"
	me.gsm = "123456789"
	MobileMessaging.mobileChat?.setUserInfo(info: me, completion: nil) // save changes asynchronously
}
```

Note that you can also provide your own `id` of a user which will identify same user across different devices:

```swift
if let me = ChatParticipant.current {
	me.id = "my-own-unique-user-id"
	MobileMessaging.mobileChat?.setUserInfo(info: me, completion: nil) // save changes asynchronously
}
```

## Handling notification taps

Mobile Messaging SDK has `notificationTapHandler` closure that takes as parameter `MTMessage` object. 
This closure will be executed when user opens the app by tapping on the notification alert. Default implementation marks the corresponding message as seen.

Default implementation can be substituted with custom implementation. Note that chat messages may be recognized by `MTMessage.isChatMessage` attribute:

```swift
MobileMessaging.notificationTapHandler = { message in
	if message.isChatMessage {
		print("Chat message with text: \(message.text) was tapped")
		<# your custom handling here #>
	}
}
```

## Message storage

### Default message storage

Mobile Chat SDK provides a built-in Core Data message storage which will store all incoming and outgoing messages. This storage is used by built-in chat view and it also has a set of comprehensive APIs which you can use in your custom message views for fetching messages:

```swift
var messagesCountersUpdateHandler: ((Int, Int) -> Void)?
func countAllMessages(completion: @escaping (Int) -> Void)
func findAllMessages(completion: FetchResultBlock)
func findMessages(withIds messageIds: [MessageId], completion: FetchResultBlock)
func findMessages(withQuery query: Query, completion: FetchResultBlock)
```

and for removing messages:

```swift
func removeAllMessages()
func remove(withIds messageIds: [MessageId])
func remove(withQuery query: Query)
```

### Custom message storage

It is also possible to use your own implementation of message storage. In order to do that, you need to implement a protocol `MessageStorage` and use a different Mobile Messaging initialization chatin:

```swift
MobileMessaging
	.withApplicationCode(<# your applicatio code #>, notificationType: <# your notifications types preferences #>)?
	.withMobileChat(storage: <# your custom MessageStorage implementation #>) // this line prepares mobile chat service and sets your custom storage as backing message storage for the chat
	.start()
```

For more information about the Message Storage see the in-code documentation for `MessageStorage` protocol, `Query` class, `MessageStorageDelegate` protocol, `MessageStorageFinders` protocol, `MessageStorageRemovers` protocol. Default Core Data message storage implementation `MMDefaultMessageStorage`.
