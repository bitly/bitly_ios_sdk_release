# Bitly iOS SDK

![Supported Platforms](https://img.shields.io/cocoapods/p/BitlySDK.svg) ![Releases](https://img.shields.io/github/release/bitly/bitly_ios_sdk_release.svg) [![Latest pod release](https://img.shields.io/cocoapods/v/BitlySDK.svg)](http://cocoapods.org/pods/BitlySDK) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) [![Build Status](https://travis-ci.com/bitly/bitly_ios_sdk.svg?token=GQk2M5gzMUKUJCESKF18&branch=master)](https://travis-ci.com/bitly/bitly_ios_sdk) [![Coverage Status](https://coveralls.io/repos/github/bitly/bitly_ios_sdk/badge.svg?t=xurvl2)](https://coveralls.io/github/bitly/bitly_ios_sdk)

## Getting Started
If you do not have any mobile apps setup in your Bitly account you will need to refer to the user guide for setting up deep links in your account.  

Once you have setup your mobile app in your account, go to Your Settings -> Advanced -> Deep Link Applications.  There you will find the Bitly App ID which needs to be included in your SDK configuration.

The domain referenced in the code examples below will be the Bitly Branded Domain which is enabled for Universal Links.

## Enable Universal Links
To create the Apple site association file on your domain you must send your Apple app ID to your Bitly Account Manager to enable.

The Apple app ID is your team ID and bundle ID joined with a period (ex - 1A234H7ABC.com.yourdomain.YourApp).  In the Apple’s Developer Portal your team ID appears on Organization Profile > Account Summary.  When you enter the Member Center on http://developer.apple.com, click you name on the top right and click on “View Account” and you will find your team ID under the Developer Account Summary.

## Install the SDK

### CocoaPods
1. Add the following to your Podfile

  ```
  pod 'BitlySDK'
  ```
2. Run the following to download and install the SDK

  ```
  pod install
  ```

### Carthage
1. Add the following to your Cartfile

  ```
  github "bitly/bitly_ios_sdk_release"
  ```
2. Run the following to download and build the framework

  ```
  carthage update
  ```
3. Drag the built `BitlySDK.framework` into your Xcode project

## Configure the Universal Links
1. In Xcode open your project file
2. Go to the Capabilities tab
3. Ensure that Associated Domains is enabled and expand it
4. Click on the + button and add your domain as `applinks:yourdomain.com`
5. Ensure that the yourprojectname.entitlements file is added to the appropriate build target

## Configure the SDK for Universal Links
1. Import the SDK in your AppDelegate

  **Swift**
  ```swift
  import BitlySDK
  ```

  **Objective-C**
  ```objective-c
  #import <BitlySDK/BitlySDK.h>
  ```
2. Initialize the SDK on application initialization

  **Swift**
  ```swift
  func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
      Bitly.initialize("YOUR_APP_ID", supportedDomains:["yourdomain.com","yourotherdomain.com"], supportedSchemes:["yourscheme"]) { response, error in
          // response provides a BitlyResponse object which contains the full URL information
          // response includes a status code
          // error provides any errors in retrieving information about the URL
          // Your custom logic goes here...
      }
      return true
  }
  ```

  **Objective-C**
  ```objective-c
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      [Bitly initialize:@"YOUR_APP_ID" supportedDomains:[NSArray arrayWithObjects: @"yourdomain.com", @"yourotherdomain.com", nil] supportedDomains:[NSArray arrayWithObjects: @"yourscheme", nil] handler:^(BitlyResponse * response, NSString * error) {
          // response provides a BitlyResponse object which contains the full URL information
          // response includes a status code
          // error provides any errors in retrieving information about the URL
          // Your custom logic goes here...
      }];
      return YES;
  }
  ```

  > By default the Bitly SDK utilizes the IDFV to identify the user. If you which to use the IDFA to track the user simply provide the `deviceId` to the appropriate method when initializing the SDK.
  > You can also provide the access token for shortening with the `accessToken` provided to the initializing methods.

3. Handle incoming links

  **Swift**
  ```swift    
  func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([Any]?) -> Void) -> Bool {
      return Bitly.handle(userActivity)
  }

  func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
      return Bitly.handleOpen(url)
  }
  ```

  **Objective-C**
  ```objective-c
  - (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void(^)(NSArray *restorableObjects))restorationHandler {
      return [Bitly handleUserActivity:userActivity];
  }

  - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey, id> *)options {
      return [Bitly handleOpenUrl:url];
  }
  ```

4. Retry on error

  **Swift**
  ```swift    
  Bitly.retryError(error)
  ```

  **Objective-C**
  ```objective-c
  [Bitly retryError:error];
  ```

  >If there are any issues in handling a Universal Link the operation can be retried using the following line of code in your response handler from step 2

## Configure the SDK for Shortening

1. Configure an OAUTH token for your app http://bitly.com/a/oauth_apps
2. Import the SDK in your AppDelegate

  **Swift**
  ```swift
  import BitlySDK
  ```

  **Objective-C**
  ```objective-c
  #import <BitlySDK/BitlySDK.h>
  ```

3. Initialize the SDK on application initialization

  **Swift**
  ```swift
  func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
      Bitly.initialize("YOUR_APP_ACCESS_TOKEN")
      return true
  }
  ```

  **Objective-C**
  ```objective-c
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      [Bitly initialize:@"YOUR_APP_ACCESS_TOKEN"];
      return YES;
  }
  ```    

    > You can combine this initialization with the App Link initialization

5. You can shorten from anywhere in your application

  **Swift**
  ```swift
    Bitly.shorten("http://theurlyouwishtoshorten.com") { response, error in
        // response provides a BitlyResponse object which contains the shortened Bitlink
        // response includes a status code
        // error provides any errors in retrieving information about the URL
        // Your custom logic goes here...
    }
  ```

  **Objective-C**
  ```objective-c
    [Bitly shorten:@"http://theurlyouwishtoshorten.com" handler:^(BitlyResponse * response, NSString * error) {
        // response provides a BitlyResponse object which contains the shortened Bitlink
        // response includes a status code
        // error provides any errors in retrieving information about the URL
        // Your custom logic goes here...
    }];
  ```    
