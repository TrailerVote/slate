---
title: TrailerVote Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - objective_c
  - swift

toc_footers:
  - © TrailerVote. CONFIDENTIAL.


search: true
---

# Introduction

The TrailerVote is a innovative software service that enables movie-ticketing apps to increases user engagement and customer understanding by encouraging moviegoers to rate trailers as they are played on the big screen of the cinema. Users are later sent notifications as a reminder that tickets for the movie they wanted see are now on sale.

Features include:

- Theatre-Optimized Audio Recognition: Optimized for complex cinema environments including 7+ speakers, reverb, echo, deep bass
- Branding & Styling: Custom branding for listening experience with your choice of background colors and logo.
- Offline audio recognition: Minimizes the need for network connectivity at the cinema.
- Custom Recognition: Identify trailer content, loyalty program promotions, or advertisements and serve the corresponding interactions.

# Requirements

In order to use TrailerVote technology, you must have the following:

- A movie-related mobile app
- Xcode 9 or higher
- iOS 10 or higher

# iOS
## Installation

1. Contact TrailerVote for the the latest iOS SDK.
2. Drag TrailerVoteSDK.framework into your Xcode project tree:
  ![TrailerVote framework included into project](/images/img_framework_in_project_tree.png "TrailerVote Framework in Xcode Project")
3. In your app Target Settings -> General tab, under the Embedded Binaries section, click the + button and select the imported TrailerVoteSDK.framework item. Click the Add button.
  ![TrailerVote added to Embedded Binaries](/images/img_framework_embedding.png "TrailerVote Embedding")

##Getting Started

```swift
import TrailerVoteSDK
```

```objective_c
#import <TrailerVoteSDK/TrailerVoteSDK.h>
```

The TrailerVote SDK has to be imported before it can be used.

</br>
</br>



```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {

        // setup the SDK credentials
        TVTrailerVoteFactory.setupCredentials(withUsername: "ReplaceThisWithAKeyWeProvide", password: "ReplaceThisWithAPasswordWeProvide")
    
        // launch the trailer recognition feature data preload
        TVTrailerVoteFactory.shared().launchDataPreload()

        // override the default partner logo image
        let partnerLogoImage = UIImage(named: "logo")
        TVTrailerVoteFactory.shared().setPartnerLogoImage(partnerLogoImage)
     
        // process a remote notifications payload, if the app was launched by tapping the notification
        if let remoteNotificationPayload = launchOptions?[.remoteNotification] as? [AnyHashable: Any] {
            TVTrailerVoteFactory.shared().processPushNotificationPayload(remoteNotificationPayload)
        }
    
        return true

    }
```

```objective_c
@interface AppDelegate : UIResponder <UIApplicationDelegate>
@property (nonatomic) UIWindow * window;
@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // setup the SDK credentials
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"ReplaceThisWithAKeyWeProvide" password:@"ReplaceThisWithAPasswordWeProvide"];
    
    // launch the trailer recognition feature data preload
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    
    // override the default partner logo image
    UIImage * partnerLogoImage = [UIImage imageNamed:@"logo"];
    [[TVTrailerVoteFactory sharedFactory] setPartnerLogoImage:partnerLogoImage];
    
    // process a remote notifications payload, if the app was launched by tapping the notification
    if (options[UIApplicationLaunchOptionsRemoteNotificationKey]) {
        NSDictionary * payload = (NSDictionary *)options[UIApplicationLaunchOptionsRemoteNotificationKey];
        [[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:payload];
    }
    
    return YES;
}
```


The `setupCredentials`, `launchDataPreload`, `setPartnerLogoImage`, and `processPushNotificationPayload` functions can be included in the `didFinishLaunchingWithOptions` function.

</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>


```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        
        // enable the remote notifications feature once the device token is received
        TVTrailerVoteFactory.shared().enablePushNotifications(withDeviceID: deviceToken.map { String(format: "%02.2hhx", $0) }.joined())
    }
    
    func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
        
        // get the SDK to process received remote notification payload
        TVTrailerVoteFactory.shared().processPushNotificationPayload(userInfo)
        
        completionHandler(.noData)
    }
}
```

```objective_c
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)devToken {
    const unsigned * tokenBytes = [devToken bytes];
    NSString * hexToken = [NSString stringWithFormat:@"%08x%08x%08x%08x%08x%08x%08x%08x",
                         ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                         ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                         ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];

     // enable the remote notifications feature once the device token is received
     [[TVTrailerVoteFactory sharedFactory] enablePushNotificationsWithDeviceID:hexToken];   
}

- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler
{
    // get the SDK to process received remote notification
    [[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:userInfo];
}

@end
```

In order to receive the notifications from TrailerVote, you have to enable notifications.
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>


```swift
TVTrailerVoteFactory.shared().logEvent("ticket-purchased", identifier: "your-movie-identifier", quantity: "number-of-tickets", value: "sum-of-ticket-prices", fees:"convenience-fees-charged", currency:"three-letter-currency-code")
```

```objective_c
[[TVTrailerVoteFactory sharedFactory] logEvent:@"ticket-purchased" identifier:@"your-cinema-identifier" quantity:@"number-of-tickets" value:@"sum-of-ticket-prices" fees:"convenience-fees-charged" currency:@"three-letter-currency-code"];
```

To determine the effectiveness of notifications and reminders, use the `logEvent` method to report the completion of a transaction (in this case a ticket purchase) to TrailerVote. These results will be reflected in the client's analytics.


##In-Theatre Voting Experience
The main feature of the SDK is the movie trailers recognition. We use the TVAudioRecognitionViewController class for presenting a full-screen user interface and for handling the recognition processes.
![alt text](/images/img_listening_screen.png "TrailerVote Listening Screen")

```objective_c
TVAudioRecognitionViewController * audioRecognitionVC = [[TVTrailerVoteFactory sharedFactory] audioRecognitionViewController];
[self presentViewController:audioRecognitionVC animated:YES completion:nil];
```

```swift
let audioRecognitionVC = TVTrailerVoteFactory.shared().audioRecognitionViewController()
present(audioRecognitionVC, animated: true, completion: nil)
```

Instantiate the view controller by calling the `[TVTrailerVoteFactory audioRecognitionViewController]` method of the main factory class:
After creating an instance, the audio recognition view controller can be easily presented using UIKit present(_:animated:completion:) method:


When the movie trailer is recognized, the voting buttons are shown with the prompt for the user to vote.
![alt text](/images/recognized_avengers.png "TrailerVote Listening Screen")
After the user votes, the feedback is recorded internally in the SDK and transmitted to TrailerVote. This means that this information is visible in the voted trailers feed and any API that exposes the vote.

Note: Special advertisement clips, such as ad banners or special action triggers are handled differently - the fullscreen web view is presented with the corresponding url being loaded or some other UI elements are presented, such as the “Put your phones away” view.

##Displaying the Watchlist

![alt text](/images/img_voted_movies_feed.png "TrailerVote Watchlist")

>To embed the view into your UI, call the 

```objective_c
[[TVTrailerVoteFactory sharedFactory] votedTrailersFeedViewControllerEmbeddedInParentViewController:parentView:]

```

```swift
TVTrailerVoteFactory.shared().votedTrailersFeedViewControllerEmbedded(inParentViewController:parentView:)
```

#Android
## Installation

1. Contact TrailerVote for the the latest Android SDK.
2. Copy `com` directory into your project libraries directory. Example: `YourAppDir/app/libs/`
 ![alt text](images/img_sdk_location.png "Android SDK location")
3. In you project `gradle` file add libraries directory to repositories list.
```java
allprojects {
    repositories {
        maven {
            url "libs"
        }
    }
}
```
4. In your app module `gradle` file add TrailerVote SDK dependency:
```java
dependencies {
    implementation "com.trailervote:trailervotesdk:1.0.0@aar"
}
```

## Getting Started

The TrailerVote SDK contains a main class ([`TrailerVoteSdk`](/android)) which contains all the methods necessary for adding the TrailerVote experience to your app.

1. Configuring and initializing the **TrailerVote SDK**
2. Enabling and configuring the **TrailerVote In-Theatre feature**
3. Enabling the **TrailerVote Video Player**
4. Enabling the **Analytics** and **Remote notifications** capabilities

## Configuring and initializing the TrailerVote SDK

The initialization process of the SDK begins immediately at the first call of `TrailerVoteSdk.init(context, backendEndpoint, username, password);`. All internal dependencies are initialized as well as public singleton instances.

To start the pre-loading process of the trailer recognition data, call the `TrailerVoteSdk.instance().load();` method.

Once the data is downloaded, the trailer recognition feature will be available in offline, but please keep the data pre-load call triggered on your app launch so that the SDK could update the recognition data.

## Enabling the TrailerVote In-Theatre feature

The main feature of the SDK is the audio recognition of movie trailers. We use the `TrailerVoteRecognitionActivity` for presenting a full-screen user interface and for handling the audio recognition process.

![TrailerVote main listening screen](/images/img_recognition_screen.jpg "TrailerVote Screenshot")

Navigate to recognition screen by calling the -`TrailerVoteSdk.instance().openRecognitionScreen(Context)` method of the main SDK class:

```java
boolean success = TrailerVoteSdk.instance().openRecognitionScreen(context);
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

To launch the video player, call the

```java
TrailerVoteSdk.instance().openVideoPlayerWithMovieIDs(context, anArrayOfMovieIDs, initialIndex);
```

Given an array of movie ids, the video player will automatically manage the playback queue of trailers and by providing the initial index you can change the initial trailer to start playback with.

## Enabling the Analytics and the Remote Notifications capabilities.

Both the analytics and the remote notifications capanilities require the client token to be provided to the SDK. To begin the setup, provide your token by calling the `setRemoteAnalyticsToken(YOUR_ANALYTICS_TOKEN)` method. The key events will be sent automatically by the SDK.

```java
TrailerVoteSdk.instance().setRemoteAnalyticsToken(YOUR_ANALYTICS_TOKEN);
```

For enabling the remote notifications capability, start by calling `enablePushNotifications(deviceToken)` and `setClassForNotificationIntent(class)` methods.

```java
FirebaseInstanceId.getInstance().getInstanceId().addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
    @Override
    public void onComplete(@NonNull Task<InstanceIdResult> task) {
        if (task.isSuccessful()) {
            TrailerVoteSdk.instance().enablePushNotifications(task.getResult().getToken());
            TrailerVoteSdk.instance().setClassForNotificationIntent(MainActivity.class);
        }
    }
}
```

When the notification is received, call the `onNotificationReceived(intentExtras, listener)` method. In order to communicate back to your app after processing the notification's extras, the SDK provides `NotificationActionListener` interface. Implement this interface to react to the notification processing result.

```java
if (intent.getAction() == Intent.ACTION_MAIN) {
  Bundle extras = intent.getExtras();
  if (extras.containsKey("trailervote_notification") {
    TrailerVoteSdk.instance().onNotificationReceived(extras, new NotificationActionListener() {
      @Override
      void onNotificationAction(@NonNull NotificationAction action, @Nullable String movieId) {
        switch (action) {
          case NotificationAction.OPEN_RECOGNITION:
            
            break;
          case NotificationAction.OPEN_MOVIE_DETAILS:
            
            break;
          case NotificationAction.OPEN_MOVIE_SHOWTIMES:
            
            break;
          case default:
            
            break;
        }
      }
    });
  }
}
```

In some time later, when you wish to stop the remote notifications capability, call the `disablePushNotifications` method to remove current device token from the notifications recipients list.

To track analytics events, the SDK provides several methods:

- `TrailerVoteSdk.instance().logTicketPurchasedEvent(movieId, quantity, totalPrice, convenienceFees, currencyCode);`
- `TrailerVoteSdk.instance().logShowtimesPageShownEvent(movieId);`

Call these methods in corresponding places in your app to submit the corresponding events.

When recognition screen is opened, the event will be submitted automatically.


#Additional Notes


```objective_c
[[TVTrailerVoteFactory sharedFactory] disablePushNotifications] 
```

```swift
TVTrailerVoteFactory.shared().disablePushNotifications()
```

In some time later, when you wish to stop the remote notifications capability, call the method to remove the current device from the notifications recipients list.

