---
title: TrailerVote Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - swift
  - kotlin
  - gradle

toc_footers:
  - © TrailerVote. CONFIDENTIAL.


search: true
---

# Introduction

The TrailerVote is an innovative software service that enables movie-ticketing apps to increases user engagement and customer understanding by encouraging moviegoers to rate trailers as they are played on the big screen of the cinema. Users are later sent notifications as a reminder that tickets for the movie they wanted see are now on sale.

Features include:

- Theatre-Optimized Audio Recognition: Optimized for complex cinema environments including 7+ speakers, reverb, echo, deep bass
- Branding & Styling: Custom branding for listening experience with your choice of background colors and logo.
- Offline audio recognition: Minimizes the need for network connectivity at the cinema.
- Custom Recognition: Identify trailer content, loyalty program promotions, or advertisements and serve the corresponding interactions.

# Requirements

In order to use TrailerVote technology, you must have the following:

**For iOS**

- A movie-related mobile app
- Xcode 9 or higher
- iOS 10 or higher

**For Android**

- A movie-related mobile app
- Android 4.4 (API 19) or higher

# iOS

## Installing the SDK using CocoaPods

```
target 'TargetNameForYourApp' do
    pod 'TrailerVoteSDK'
end
```

In your `Podfile` add the following code:

```
pod repo update && pod install
```

Then install the Pod by running the following command in Terminal. Note the `pod repo update` being called first to ensure that the latest version of the SDK will be installed.

After the installation is completed, open the `.xcworkspace` file.

## Credentials setup

```
<key>TrailerVoteSDKAuthToken</key>
<string>YOUR_AUTH_TOKEN</string>
```

Add the `TrailerVoteSDKAuthToken` to your app `Info.plist`. Don't forget to insert your auth token!

## SDK Initialization

```swift
import UIKit
import TrailerVoteSDK

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        /// Standard configuration for TrailerVote. Advanced options available at `TVPreloadMode` enum
        TVTrailerVoteFactory.setPreloadMode(.normal) 
        
        /// Provides SDK context of app launch options
        TVTrailerVoteFactory.application(application, didFinishLaunchingWithOptions: launchOptions ?? [:]) 
        
        /// Adds handler to process notifications from TrailerVote SDK
        TVTrailerVoteFactory.shared().remoteNotificationsDelegate = self 

        ///
        /// Your app does what it needs to for 1st screen experience here. 
        ///

        /// Instructs TrailerVoteSDK it's okay to begin preloading content.
        TVTrailerVoteFactory.shared().launchDataPreload()

        // Forwards the push notification payload to the SDK if the app was opened from a notification
        if let remoteNotificationPayload = launchOptions?[.remoteNotification] as? [AnyHashable: Any] {
            TVTrailerVoteFactory.shared().processPushNotificationPayload(remoteNotificationPayload)
        }

        UNUserNotificationCenter.current().delegate = self
        application.registerForRemoteNotifications()
        
        return true
    }


    /// Starts by registering your app to remote notifications in your `AppDelegate` and requesting the user permission.
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (result, error) in
            if !result || error != nil {
                TVTrailerVoteFactory.shared().disablePushNotifications()
            } else {
                let tokenString = deviceToken.map { String(format: "%02.2hhx", $0) }.joined()
                TVTrailerVoteFactory.shared().setRemoteNotificationsDeviceToken(tokenString)
            }
        }
    }
}


extension AppDelegate: UNUserNotificationCenterDelegate {
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        completionHandler([.alert, .badge, .sound])
    }

    /// Forwards the notification to the SDK so that the SDK can process the received notification payload.
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        if response.actionIdentifier == UNNotificationDefaultActionIdentifier {
            TVTrailerVoteFactory.shared().processPushNotificationPayload(response.notification.request.content.userInfo)
        }
        completionHandler()
    }
}

extension AppDelegate: TVRemoteNotificationsDelegate {
    func openShowtimes(forMovieID movieID: Int) {
        /// Add notes
    }

    func openTrailerRecognition() {
        /// Add notes
    }

    func presentWebView(for url: URL) {
        /// Add notes
    }
}
```

Copy and paste to integrate the following code into your application.

## In-Cinema Experience

```
<key>NSMicrophoneUsageDescription</key>
<string>Microphone access is required for the In-Cinema experience</string>
```

Add the `NSMicrophoneUsageDescription` key to your `Info.plist`:

```swift
import UIKit
import TrailerVoteSDK

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        let button = TVTrailerVoteFactory.shared().inCinemaExperienceButton(withHostViewController: self, recognitionDelegate: self)
        button.backgroundColor = .black
        button.addTarget(self, action: #selector(ViewController.handleLaunchButtonTap), for: .touchUpInside)

        view.addSubview(button)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }

    @objc private func handleLaunchButtonTap() {
        TVTrailerVoteFactory.shared().presentTrailerRecognitionViewController(self, recognitionDelegate: self)
    }
}

extension ViewController: TVAudioRecognitionViewControllerNewDelegate {
    func audioRecognitionViewController(_ viewController: UIViewController, userDidTapOnFacebookLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool, hostViewController: UIViewController) {
        // process facebook login
    }

    func audioRecognitionViewController(_ viewController: UIViewController, userDidTapOnTwitterLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool, hostViewController: UIViewController) {
        // process twitter login
    }    
}

```

Copy the following code into your project. The Trailer Recognition feature of the SDK provided as a ready-to-use button that can be embedded into your UI. Simply call the corresponding method of the SDK to embed the button, then present the screen in response to tap.

## Video player

```swift
import UIKit
import TrailerVoteSDK

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let button = UIButton(frame: .zero)
        button.setTitle("Launch Video Player", for: .normal)
        button.backgroundColor = .black

        button.addTarget(self, action: #selector(ViewController.handleLaunchButtonTap), for: .touchUpInside)

        view.addSubview(button)
        button.translatesAutoresizingMaskIntoConstraints = false
        button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }
    
    @objc private func handleLaunchButtonTap() {
        TVTrailerVoteFactory.shared().presentVideoPlayerViewController(self, movieTrailerURL: <MOVIE_TRAILER_URL>)
    }
}

extension ViewController: TVAudioRecognitionViewControllerNewDelegate {
    func audioRecognitionViewController(_ viewController: UIViewController, userDidTapOnFacebookLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool, hostViewController: UIViewController) {
        // process facebook login
    }

    func audioRecognitionViewController(_ viewController: UIViewController, userDidTapOnTwitterLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool, hostViewController: UIViewController) {
        // process twitter login
    }
}
```

TrailerVoteSDK allows for capturing people's interest in trailers right from the video player. Replacing your video player with ours is easy: simply call the following SDK method providing the host view controller to present the video player screen from as well as a trailer URL to use:

## Attribution

```swift
TVTrailerVoteFactory.shared().logShowtimesPageShownEvent(withIdentifier:<MOVIE_ID>)

TVTrailerVoteFactory.shared().logTicketPurchasedEvent(withMovieID:<MOVIE_ID>, showtimeDate:<SHOWTIME_DATE>, ticketCount:<TICKET_COUNT>, totalPrice:<TOTAL_PRICE>)
```

Two methods must be added to your code to display the number of showtime page opens and ticket purchases related to TrailerVote. These two singles lines must be copied to the final step of each respective method:



#Android
## Android Installation

```gradle
repositories {
  maven {
    url "https://pods.dev.trailervote.com/android/"
  }
}

dependencies {
  implementation ('com.trailervote:trailervote-sdk:1.2.0@aar') {
    transitive = true
  }
}
```

Add the TrailerVoteSDK dependency to your build.gradle file:

## Getting Started

The TrailerVote SDK contains a main class ([`TrailerVoteSdk`](/#android)) which contains all the methods necessary for adding the TrailerVote experience to your app.

```kotlin
import com.trailervote.trailervotesdk.TrailerVoteSdk
```

Import the SDK to use the public interface and call methods.

1. Configuring and initializing the **TrailerVote SDK**
2. Enabling and configuring the **TrailerVote In-Theatre feature**
3. Enabling the **TrailerVote Video Player**
4. Enabling the **Analytics** and **Remote notifications** capabilities

## Configuring and initializing the TrailerVote SDK

```kotlin
TrailerVoteSdk.init(context, authority, endpoint, username, password)
// or
TrailerVoteSdk.init(context, authority, endpoint, username, password, imageLoadingMode)
```

The initialization process of the SDK begins immediately at the first call of the `init` method. All internal dependencies are initialized as well as public singleton instances.

```kotlin
TrailerVoteSdk.instance().loadData()
// or
TrailerVoteSdk.instance().loadData(delayInMilliseconds)
```

To start the pre-loading process of the trailer recognition data, call the `loadData` method.

Once the data is downloaded, the trailer recognition feature will be available in offline, but please keep the data pre-load call triggered on your app launch so that the SDK could update the recognition data.

## Enabling the TrailerVote In-Theatre feature

The main feature of the SDK is the audio recognition of movie trailers. We use the `TrailerVoteRecognitionActivity` for presenting a full-screen user interface and for handling the audio recognition process.

![TrailerVote main listening screen](/images/img_recognition_screen.jpg "TrailerVote Screenshot")

Navigate to recognition screen by calling the `openRecognitionScreen` method of the main SDK class:

```kotlin
val success = TrailerVoteSdk.instance().openRecognitionScreen(context)
if (!success) {
  // error
}
```

Note that after a trailer is recognized the SDK will render the voting buttons automatically and prompt the user to vote.

![TrailerVote has detected a trailer and has presented a voting card](/images/img_recognition_screen_voting.jpg "TrailerVote Voting Prompt")

After a user votes, the feedback is recorded internally in the SDK and transmitted to TrailerVote. This means that this information is visible in the voted trailers feed and any API that exposes the vote.

*Note: Special advertisement clips are handled differently - the fullscreen `WebView` is presented with the corresponding url being loaded.*

The SDK provides the ways to set the logo image displayed at the trailer recognition screen. To set the logo image, add the drawable with the name `img_recognition_screen_partner_logo`.
You can override the default voting card background as well by adding the drawable with the name `img_recognition_screen_partner_background`.

## Enabling the TrailerVote Video Player

![TrailerVote Voting buttons on Video Player](/images/android_img_player_screen.png "TrailerVote Video Player")

Because moviegoers watch trailers in your movie app, we recommend replacing your video player with the **TrailerVote Video Player**. The TrailerVote Video Player will provide a prompt during the video playback.

To launch the video player, call the `openVideoPlayerForTrailer` method.

```kotlin
TrailerVoteSdk.instance().openVideoPlayerForTrailer(context, trailerUrl)
```

Given an array of movie ids, the video player will automatically manage the playback queue of trailers and by providing the initial index you can change the initial trailer to start playback with.

## Enabling the Analytics and the Notifications capabilities.

```kotlin
TrailerVoteSdk.instance().setClassForNotificationIntent(MainActivity::class.java)
```

Set the class for notification intent for enabling local notifications.

```kotlin
public override fun onNewIntent(intent: Intent) {
    this.intent = intent
    if (intent.action == Intent.ACTION_MAIN) {
        val extras = intent.extras
        if (extras?.containsKey("trailervote_notification") == true) {
            TrailerVoteSdk.instance().onNotificationReceived(extras) { action, param ->
                when (action) {
                    TrailerVoteSdk.NotificationAction.OPEN_RECOGNITION -> {
                      //
                    }
                    TrailerVoteSdk.NotificationAction.OPEN_MOVIE_DETAILS -> {
                      val movieId = param
                      //
                    }
                    TrailerVoteSdk.NotificationAction.OPEN_URL -> {
                      val url = param
                      //
                    }
                    TrailerVoteSdk.NotificationAction.NONE -> {
                      //
                    }
                }
            }
        }
    }
}
```

When the notification is received, call the `onNotificationReceived` on the TrailerVoteSDK instance. In order to communicate back to your app after processing the notification's extras, the SDK provides `NotificationActionListener` interface. Implement this interface to react to the notification processing result.

To track analytics events, the SDK provides several methods. The key events will be sent automatically by the SDK.

```kotlin
TrailerVoteSdk.instance().logTicketPurchasedEvent(movieId, quantity, totalPrice, convenienceFees, currencyCode)
TrailerVoteSdk.instance().logShowtimesPageShownEvent(movieId)
```

Call these methods in corresponding places in your app to submit the corresponding events.

When recognition screen is opened, the event will be submitted automatically.
