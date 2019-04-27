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
2. Drag **TrailerVoteSDK.framework** into your Xcode project tree:
  ![alt text](/images/img_framework_in_project_tree.png "TrailerVote Framework in Xcode Project")
3. In your app **Target Settings** -> **General** tab, under the **Embedded Binaries** section, click the **+** button and select the imported **TrailerVoteSDK.framework** item. Click the **Add** button.
  ![alt text](/images/img_framework_embedding.png "TrailerVote Embedding")

##Getting Started

```swift
import TrailerVoteSDK
```
```objective_c
#import <TrailerVoteSDK/TrailerVoteSDK.h>
```

Import the SDK to use the public interface and call methods.

<br />
<br />
<br />

```swift
TVTrailerVoteFactory.shared().methodName()
```
```objective_c
[[TVTrailerVoteFactory sharedFactory] methodName];
```

TrailerVote SDK provides the main factory class `TVTrailerVoteFactory`, containing various methods for adding the TrailerVote experience to your app.

Simply call the `[TVTrailerVoteFactory sharedFactory]`/`TVTrailerVoteFactory.shared()` class method to access the singleton instance.

## Configuring and initializing the TrailerVote SDK

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
    return true
}
```

In order to use the SDK, you are required to provide the set of credentials (username/password). This can be done by calling the `[TVTrailerVoteFactory setupCredentialsWithUsername:password:]`/`TVTrailerVoteFactory.setupCredentials(withUsername:password:)` method.

Please note that the invocation of this method should precede any other calls on the SDK factory class, otherwise an exception will be thrown indicating the absence of credentials.

The initialization process of the SDK begins immediately upon the first invocation of `[TVTrailerVoteFactory sharedFactory]`/`TVTrailerVoteFactory.shared()` method. All internal dependencies and SDK resources are initialized automatically.

In order for the **TrailerVote In-Theatre feature** to work offline, the movie trailers recognition data needs to be downloaded from the network.

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
    TVTrailerVoteFactory.shared().launchDataPreload()
    return true
}
```

To start the pre-loading process of the trailer recognition data, call the `[[TVTrailerVoteFactory sharedFactory] launchDataPreload]`/`TVTrailerVoteFactory.shared().launchDataPreload()` method.

Once the data is downloaded, the trailer recognition feature will be available in offline, but please keep the data pre-load call triggered on your app launch so that the SDK could update the recognition data.

## Enabling and configuring the TrailerVote In-Theatre feature

```objective_c
@interface ViewController : UIViewController <TVAudioRecognitionViewControllerNewDelegate>
@end

@implementation ViewController
- (void)launchTrailerRecognition
{
    [[TVTrailerVoteFactory sharedFactory] presentTrailerRecognitionViewController:self recognitionDelegate:self];
}

- (void)                audioRecognitionViewController:(nonnull UIViewController *)viewController
userDidTapOnFacebookLoginButtonWithSubscriptionEnabled:(BOOL)subscriptionEnabled
                                    hostViewController:(nonnull __kindof UIViewController *)hostViewController
{
    //proceed with login
}

- (void)               audioRecognitionViewController:(nonnull UIViewController *)viewController
userDidTapOnTwitterLoginButtonWithSubscriptionEnabled:(BOOL)subscriptionEnabled
                                   hostViewController:(nonnull __kindof UIViewController *)hostViewController
{
    //proceed with login
}
@end
```
```swift
class ViewController: UIViewController, TVAudioRecognitionViewControllerNewDelegate {
    func launchTrailerRecognition() {
        TVTrailerVoteFactory.shared().presentTrailerRecognitionViewController(self, recognitionDelegate: self)
    }
    
    func audioRecognitionViewController(_ viewController: UIViewController,
                                        userDidTapOnFacebookLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool,
                                        hostViewController: UIViewController) {
        //proceed with login
    }
    
    func audioRecognitionViewController(_ viewController: UIViewController,
                                        userDidTapOnTwitterLoginButtonWithSubscriptionEnabled subscriptionEnabled: Bool,
                                        hostViewController: UIViewController) {
        //proceed with login
    }
}
```

The main feature of the SDK is the movie trailers recognition. To present the full-screen UI of the feature, call the `[[TVTrailerVoteFactory sharedFactory] presentTrailerRecognitionViewController:recognitionDelegate:]`/`TVTrailerVoteFactory.shared().presentTrailerRecognitionViewController(_:recognitionDelegate:)` method.

<img src="img_recognition_screen.png" />

When the movie trailer is recognized, the voting buttons are shown with the prompt for the user to vote.

<img src="img_recognition_screen_voting.png" />

After the user votes, the feedback is recorded internally in the SDK and transmitted to TrailerVote. This means that this information is visible in the voted trailers feed and any API that exposes the vote.

*Note: Special advertisement clips, such as ad banners or special action triggers are handled differently - the fullscreen web view is presented with the corresponding url being loaded or some other UI elements are presented, such as the "Put your phones away" view.*

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    [[TVTrailerVoteFactory sharedFactory] setPartnerLogoImage:<YOUR_IMAGE>];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
    TVTrailerVoteFactory.shared().launchDataPreload()
    TVTrailerVoteFactory.shared().setPartnerLogoImage(<YOUR_IMAGE>)
    return true
}
```

The SDK provides the ability to set the logo image displayed on the initial movie card. Call the `[[TVTrailerVoteFactory sharedFactory] setPartnerLogoImage:]`/`TVTrailerVoteFactory.shared().setPartnerLogoImage(_:)` method providing your own logo image to use.

## Presenting the TrailerVote Video Player

<img src="img_video_player.png" />

Because moviegoers watch trailers in your movie app, we recommend replacing your video player with the **TrailerVote Video Player**. The TrailerVote Video Player will provide a prompt for voting during the video playback. 

```objective_c
@interface ViewController : UIViewController
@end

@implementation ViewController
- (void)launchVideoPlayer
{
    [self presentViewController:[[TVTrailerVoteFactory sharedFactory] videoPlayerViewController] animated:YES];
}
@end
```
```swift
class ViewController: UIViewController {
    func launchVideoPlayer() {
        self.present(TVTrailerVoteFactory.shared().videoPlayerViewController(), animated: true)
    }
}
```

To get the video player instance, call the `[[TVTrailerVoteFactory sharedFactory] videoPlayerViewController]`/`TVTrailerVoteFactory.shared().videoPlayerViewController()` method. The video player will automatically manage the playback queue and present the voting UI in order for user to vote on shown movies.

## Integrating the Movies carousel view

<img src="img_movies_carousel.png" />

```objective_c
@interface ViewController : UIViewController
@property (nonatomic, weak) IBOutlet UIView * movieCarouselContainerView;
@end

@implementation ViewController
{
    TVProductCarouselViewController * _productCarouselVC;
}

- (void)viewDidLoad
{
    [super viewDidLoad];
    _productCarouselVC = [[TVTrailerVoteFactory sharedFactory] productCarouselViewControllerEmbeddedInParentViewController:self parentView:self.movieCarouselContainerView];
}
@end
```
```swift
class ViewController: UIViewController {
    @IBOutlet weak var movieCarouselContainerView: UIView!
    private var productCarouselVC: TVProductCarouselViewController!

    override func viewDidLoad() {
        super.viewDidLoad()
        productCarouselVC = TVTrailerVoteFactory.shared().productCarouselViewControllerEmbedded(inParentViewController: self, parentView: movieCarouselContainerView)
    }
}
```

The SDK provies the ready for use movies carousel view that can be easily integrated into your UI by using the `[[TVTrailerVoteFactory sharedFactory] productCarouselViewControllerEmbeddedInParentViewController:parentView:]`/`TVTrailerVoteFactory.shared().productCarouselViewControllerEmbedded(inParentViewController:parentView:)` method. The view incapsules the necessary logic for fetching the movies list, presenting the data for each movie as well as launching the **TrailerVote Video Player** upon the selection of the particular item in the feed.

## Integrating the Voted movies feed view

<img src="img_voted_movies_feed.png" />

```objective_c
@interface ViewController : UIViewController
@property (nonatomic, weak) IBOutlet UIView * votedMoviesFeedContainerView;
@end

@implementation ViewController
{
    TVVotedTrailersFeedViewController * _votedTrailersFeedVC;
}

- (void)viewDidLoad
{
    [super viewDidLoad];
    _votedTrailersFeedVC = [[TVTrailerVoteFactory sharedFactory] votedTrailersFeedViewControllerEmbeddedInParentViewController:self parentView:self.votedMoviesFeedContainerView];
}
@end
```
```swift
class ViewController: UIViewController {
    @IBOutlet weak var votedMoviesFeedContainerView: UIView!
    private var votedTrailersFeedVC: TVVotedTrailersFeedViewController!

    override func viewDidLoad() {
        super.viewDidLoad()
        votedTrailersFeedVC = TVTrailerVoteFactory.shared().votedTrailersFeedViewControllerEmbedded(inParentViewController: self, parentView: votedTrailersFeedContainerView)
    }
}
```

The SDK also provides the voted movies feed view for presenting the list of movies the user has previously voted on. To embed the view into your UI, call the `[[TVTrailerVoteFactory sharedFactory] votedTrailerFeedViewControllerEmbeddedInParentViewController:parentView:]`/`TVTrailerVoteFactory.shared().votedTrailerFeedViewControllerEmbedded(inParentViewController:parentView:)` method.

<img src="img_voted_movies_feed_filter.png" />

The view incapsulates the necessary logic for fetching the voted movies list, provides the capability of filtering the movies by the vote type (all, positive, neutral or negative), as well as launching the **TrailerVote Video Player** upon the selection of the particular item in the feed.

## Enabling the Analytics and the Remote Notifications capabilities

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    [TVTrailerVoteFactory setupAnalyticsToken:@"YOUR_ANALYTICS_TOKEN"];
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    return YES;
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
    TVTrailerVoteFactory.setupAnalyticsToken("YOUR_ANALYTICS_TOKEN")
    TVTrailerVoteFactory.shared().launchDataPreload()
    return true
}
```

Both the analytics and the remote notifications capabilities require the client token to be provided to the SDK. To begin the setup, provide your token by calling the `[TVTrailerVoteFactory setupAnalyticsToken:]`/`TVTrailerVoteFactory.setupAnalyticsToken(_:)` method. The key events will be sent automatically by the SDK.

<br />
<br />
<br />

```objective_c
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
{
    const char *data = [deviceToken bytes];
    NSMutableString *token = [NSMutableString string];
    for (NSUInteger i = 0; i < [deviceToken length]; i++)
    {
        [token appendFormat:@"%02.2hhX", data[i]];
    }
    NSString * tokenString = [token copy];
    [[TVTrailerVoteFactory sharedFactory] enablePushNotificationsWithDeviceID:tokenString];
}
```
```swift
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
  TVTrailerVoteFactory.shared().enablePushNotifications(withDeviceID: deviceToken.map { String(format: "%02.2hhx", $0) }.joined())
}
```

For enabling the remote notifications capability, start by calling the `[[TVTrailerVoteFactory sharedFactory] enablePushNotificationsWithDeviceID:]`/`TVTrailerVoteFactory.shared().enablePushNotifications(withDeviceID:)` method. The `deviceID` parameter is the hexadecimal string retrieved from the device token provided by the iOS in your application's delegate class `-application:didRegisterForRemoteNotificationsWithDeviceToken:`/ `application(_:didRegisterForRemoteNotificationsWithDeviceToken:)` method.

```objective_c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    [TVTrailerVoteFactory setupAnalyticsToken:@"YOUR_ANALYTICS_TOKEN"];
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    id remoteNotificationPayload = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];
    if (remoteNotificationPayload && [remoteNotificationPayload isKindOfClass:NSDictionary.class])
    {
        [[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:(NSDictionary *)remoteNotificationPayload];
    }
    return YES;
}

- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
{
    [[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:userInfo];
    completionHandler(UIBackgroundFetchResultNoData);
}
```
```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
    TVTrailerVoteFactory.setupAnalyticsToken("YOUR_ANALYTICS_TOKEN")
    TVTrailerVoteFactory.shared().launchDataPreload()
    if let remoteNotificationPayload = launchOptions?[.remoteNotification] as? [AnyHashable: Any] {
        TVTrailerVoteFactory.shared().processPushNotificationPayload(remoteNotificationPayload)
    }
    return true
}

func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    TVTrailerVoteFactory.shared().processPushNotificationPayload(userInfo)
    completionHandler(.noData)
}
```

Upon receiving the remote notification's payload dictionary in `-application:didReceiveRemoteNotification:fetchCompletionHandler:`/`application(_:didReceiveRemoteNotification:completionHandler:)` or `-application:didFinishLaunchingWithOptions:`/`application(_:didFinishLaunchingWithOptions:)` method, call the `[[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:]`/`TVTrailerVoteFactory.shared().TVTrailerVoteFactory.shared().processPushNotificationPayload(_:)` method in order for the SDK to process and react accordingly to the notification's payload data.

<br />
<br />
<br />

```objective_c
@interface AppDelegate : UIResponder <UIApplicationDelegate, TVRemoteNotificationsDelegate>
@property (strong, nonatomic) UIWindow *window;
@end

@implementation AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 
{
    [TVTrailerVoteFactory setupCredentialsWithUsername:@"YOUR_USERNAME"password:@"YOUR_PASSWORD"];
    [TVTrailerVoteFactory setupAnalyticsToken:@"YOUR_ANALYTICS_TOKEN"];
    [[TVTrailerVoteFactory sharedFactory] launchDataPreload];
    [[TVTrailerVoteFactory sharedFactory].remoteNotificationsDelegate = self;
    return YES;
}

- (void)openShowtimesForMovieID:(NSInteger)movieID
{
    // navigate to the corresponding movie showtimes screen
}
@end
```
```swift
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, TVRemoteNotificationsDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        TVTrailerVoteFactory.setupCredentials(withUsername:"YOUR_USERNAME" password:"YOUR_PASSWORD")
        TVTrailerVoteFactory.setupAnalyticsToken("YOUR_ANALYTICS_TOKEN")
        TVTrailerVoteFactory.shared().launchDataPreload()
        TVTrailerVoteFactory.shared().remoteNotificationsDelegate = self
    }
    
    func openShowtimes(forMovieID movieID: Int) {
        // navigate to the corresponding movie showtimes screen
    }
}
```

In order to communicate back to your app after processing the notification's payload, the SDK provides the `TVRemoteNotificationsDelegate` protocol. Implement this protocol in your app's delegate class and set the `delegate` property of SDK's factory class to react to the payload processing results.

In some time later, when you wish to stop the remote notifications capability, call the `[[TVTrailerVoteFactory sharedFactory] disablePushNotifications]`/`TVTrailerVoteFactory.shared().disablePushNotifications()` method to remove the current device ID from the notifications recipients list.

To track analytics events, the SDK provides several methods:

- `-(void)logShowtimesPageShownEventWithIdentifier:(nonnull NSString *)movieIdentifier;`
- `-(void)logTicketPurchasedEventWithIdentifier:(nonnull NSString *)movieIdentifier quantity:(NSUInteger)quantity value:(double)totalPrice fees:(double)convenienceFees currency:(nonnull NSString *)currencyCode;`

Call these methods in corresponding places in your app to submit the corresponding events.