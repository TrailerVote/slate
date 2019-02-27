---
title: TrailerVote Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript
  - objective_c
  - swift

toc_footers:
  - © TrailerVote

includes:
  - errors

search: true
---

# Introduction

The TrailerVote is a proprietary software library that enables movie-ticketing apps to increases user engagement and customer understanding by encouraging moviegoers to rate trailers as they are played on the big screen of the cinema, and/or rate trailers when watched on the phone. Users are later sent push notification reminders to buy tickets via the app when the movie opens in theatres.

Features include:

- Theatre-Optimized Audio Recognition: Optimized for complex cinema environments including 7+ speakers, reverb, echo, deep bass
- Branding & Styling: Custom branding for listening experience with your choice of background colors and logo.
- Offline audio recognition: Minimizes the need for network connectivity at the cinema by caching content
- Custom Recognition: Identify trailer content, loyalty program promotions, or advertisements and serve the corresponding interactions.

# Requirements

In order to use TrailerVote technology, you must have the following:

- A mobile app
- Xcode 9 or higher
- iOS 10 or higher

# iOS
## Getting Started

1. Download & unzip the latest iOS SDK from https://trailervote.com/downloads/ios-sdk.
2. Drag TrailerVoteSDK.framework into your Xcode project tree:
  ![alt text](/images/img_framework_in_project_tree.png "TrailerVote Framework in Xcode Project")
3. In your app Target Settings -> General tab, under the Embedded Binaries section, click the + button and select the imported TrailerVoteSDK.framework item. Click the Add button.
  ![alt text](/images/img_framework_embedding.png "TrailerVote Embedding")

```swift
import TrailerVoteSDK
```

The TrailerVote SDK has to be imported before it can be used.



## Authentication

> Simply call the class method to access the singleton instance.

```swift
TVTrailerVoteFactory.setupCredentials(withUsername: "ReplaceThisWithAKeyWeProvide",
                                          password: "ReplaceThisWithAPasswordWeProvide")
TVTrailerVoteFactory.setupAnalyticsToken("ReplaceThisWithATokenWeProvide")
```

```objective_c
[TVTrailerVoteFactory setupCredentialsWithUsername:@"ReplaceThisWithAKeyWeProvide"
                                          password:@"ReplaceThisWithAPasswordWeProvide"];

```

The SDK requires that credentials be provided via the setupCredentials and setupAnalyticsToken methods before use. Please note that the invocation of this method should precede any other calls on the SDK factory class, otherwise an exception will be thrown indicating the absence of credentials.


## Preloading the content


```objective_c
[[TVTrailerVoteFactory sharedFactory] launchDataPreload]
```

```swift
TVTrailerVoteFactory.shared().launchDataPreload()
```

The TrailerVote SDK To start the pre-loading process of the trailer recognition data, call the launchDataPreload method. Once the data is downloaded, the trailer recognition feature will be available in offline.



##In-Theatre Voting Experience
The main feature of the SDK is the movie trailers recognition. We use the TVAudioRecognitionViewController class for presenting a full-screen user interface and for handling the recognition processes.
![alt text](/images/img_recognition_screen.png "TrailerVote Listening Screen")

```objective_c
TVAudioRecognitionViewController * audioRecognitionVC = [[TVTrailerVoteFactory sharedFactory] audioRecognitionViewController];
```

```swift
let audioRecognitionVC = TVTrailerVoteFactory.shared().audioRecognitionViewController()
```

Instantiate the view controller by calling the [TVTrailerVoteFactory audioRecognitionViewController] method of the main factory class:


```objective_c
[self presentViewController:audioRecognitionVC animated:YES completion:nil];
```

```swift
present(audioRecognitionVC, animated: true, completion: nil)
```

After creating an instance, the audio recognition view controller can be easily presented using UIKit present(_:animated:completion:) method:


When the movie trailer is recognized, the voting buttons are shown with the prompt for the user to vote.
![alt text](/images/img_recognition_screen_voting.png "TrailerVote Listening Screen")
After the user votes, the feedback is recorded internally in the SDK and transmitted to TrailerVote. This means that this information is visible in the voted trailers feed and any API that exposes the vote.

Note: Special advertisement clips, such as ad banners or special action triggers are handled differently - the fullscreen web view is presented with the corresponding url being loaded or some other UI elements are presented, such as the “Put your phones away” view.

##Logo & Background customization

```objective_c
[[TVTrailerVoteFactory sharedFactory] setPartnerLogoImage:]
```

```swift
TVTrailerVoteFactory.shared().setPartnerLogoImage(_:)
```

The SDK provides the ability to set the logo image displayed on the initial movie card. Call the method providing your own logo image to use.


```objective_c
[[TVTrailerVoteFactory sharedFactory] setDefaultVotingCardBackgroundImage:]
```

```swift
TVTrailerVoteFactory.shared().setDefaultVotingCardBackgroundImage(_:)
```

You can override the default voting card background as well by calling the method providing your own background image to use.



##Video Player with Voting

![alt text](/images/img_video_player.png "TrailerVote Video Player")

> To get the video player instance, call the method. The video player will automatically manage the playback queue.

```objective_c
[[TVTrailerVoteFactory sharedFactory] videoPlayerViewController]
```

```swift
TVTrailerVoteFactory.shared().videoPlayerViewController()
```

Because moviegoers can watch trailers in your movie app, we believe its best to provide them the option to note which movies they wish to see or not. We recommend replacing your video player with the TrailerVote Video Player. The TrailerVote Video Player will provide a prompt for voting during the video playback.


##Carousel of Movie Trailers

![alt text](/images/img_movies_carousel.png "TrailerVote Movies Carousel")

```objective_c
[[TVTrailerVoteFactory sharedFactory] productCarouselViewControllerEmbeddedInParentViewController:parentView:]
```

```swift
TVTrailerVoteFactory.shared().productCarouselViewControllerEmbedded(inParentViewController:parentView:)
```


If you wish to feature a carousel full of movie trailers and the ability to watch and vote on them, the TrailerVote SDK has this feature built in. The movies carousel view can easily be integrated into your UI by using the following method. The view includes the necessary logic for fetching the movies list from all the trailers you have provided TrailerVote. Tapping on a carousel item will launch the TrailerVote Video Player, playing the video and show the voting buttons.

##Displaying the Watchlist

![alt text](/images/img_voted_movies_feed.png "TrailerVote Watchlist")

>To embed the view into your UI, call the 

```objective_c
[[TVTrailerVoteFactory sharedFactory] votedTrailersFeedViewControllerEmbeddedInParentViewController:parentView:]

```

```swift
TVTrailerVoteFactory.shared().votedTrailersFeedViewControllerEmbedded(inParentViewController:parentView:)
```


The SDK also provides the voted movies feed view for presenting the list of movies the user has previously voted on. The view encapsulates the necessary logic for fetching the voted movies list, provides the capability of filtering the movies by the vote type (all, positive, neutral or negative), as well as launching the TrailerVote Video Player upon the selection of the particular item in the feed.

 
##Analytics and Notifications

```objective_c
[TVTrailerVoteFactory setupAnalyticsToken:]

```

```swift
TVTrailerVoteFactory.setupAnalyticsToken(_:)
```



Both the analytics and the remote notifications capabilities require the client token to be provided to the SDK. To begin the setup, provide your token by calling the method. The key events will be sent automatically by the SDK.

```objective_c
[[TVTrailerVoteFactory sharedFactory] enablePushNotificationsWithDeviceID:]


```

```swift
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
  TVTrailerVoteFactory.shared().enablePushNotifications(withDeviceID: deviceToken.map { String(format: "%02.2hhx", $0) }.joined())
}
```

For enabling the remote notifications capability, start by calling the method. The deviceID parameter is the hexadecimal string retrieved from the device token provided by the iOS in your application’s delegate class application(application:didRegisterForRemoteNotificationsWithDeviceToken:) method.

```objective_c
[[TVTrailerVoteFactory sharedFactory] processPushNotificationPayload:]
```

```swift
TVTrailerVoteFactory.shared().processPushNotificationPayload(_:)
```

Upon receiving the remote notification’s payload dictionary in application(_:didReceiveRemoteNotification:completionHandler:) or application(_:didFinishLaunchingWithOptions:) method call the method in order for the SDK to process and react accordingly to the notification’s payload data.


```objective_c
[[TVTrailerVoteFactory sharedFactory] disablePushNotifications] 
```

```swift
TVTrailerVoteFactory.shared().disablePushNotifications()
```

In some time later, when you wish to stop the remote notifications capability, call the method to remove the current device from the notifications recipients list.




# Android



# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

