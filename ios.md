# PayPal Native Checkout SDK for iOS 9 and 10

# 

The PayPal Native Checkout SDK for iOS, provides a simplified way for you to provide a secure and safe payment solution for your apps. 

* **Native Code Current Version** : 1.0.0
* **Experience Bundle Version** : 1.0.0
* **Experience** : Safari View Controller

For detailed version history please see our [Change Log](changelog.md).

# 

### Table of Contents

* [Getting Started](#getting-started)
* [Obtaining a PayPal Client ID](#paypal-client-id)
    * [CocoaPods](#cocoapods)
    * [Carthage](#carthage)
    * [Direct Download](#direct-download)
* [Integrating the SDK](#integrating-the-checkout-experience)
    * [**Integration Type**: WebView Store](#webview-store)
        * [Setting Up an iOS Deep Link](#setting-up-deep-linking)
        * [Setup SDK Required Settings](#setting-up-sdk-parameters)
* [Invoking  the PayPal Native Checkout SDK](#invoking-the-sdk)
    * [Quick / Auto Integration](#quickauto-integration)
    * [Manual Integration](#manual-integration)

# 

## Getting Started


The PayPal Native SDK is available through `CocoaPods` `Carthage` and as a standalone `.Framework` for `XCode`. Please note that the Framework shipped in our current preview in the `Frameworks` folder is `Fat Binary` and can not be directly submitted to the App Store without being Stripped of Simulator bits, please use the `Zip` archive in the [direct download](#direct-download) for a arm/arm64 only build.

This SDK ships with React Native. If you are currently using React Native in your application, it will likely collide. Please reach out to us, we'd love to work with you while we sandbox React Native away from the SDK.

# 

### PayPal Client ID

Before you start, you should have already obtained a PayPal Client ID from the PayPal Developer portal. If you haven't done that step yet, please stop here, and follow the directions in our [Obtaining a PayPal Client ID](how_to_merchantid.md) docs.

# 

### CocoaPods

Simply add `paypal-nativecheckout` to your `Podfile` as a git repo for the developer preview, and execute the `pod install` command on your project directory. This will link the `NativeCheckout.framework` against your project.

```ruby
pod ‘paypal-nativecheckout’, :git => 'git@github.com:paypal/paypalcheckout-ios.git', :branch => 'master'
```
# 

### Carthage 

Getting started with Carthage is easy, simply add our public `paypal-xo` repository to your Carthage file, and run `carthage update`. Our `.Framework` will be available for you to import into `XCode`.


```bash
github "PayPal/PayPalNativeXO" >= 1.0.0
```
# 

### Direct Download

If you would simply like to get started with the PayPal Native Checkout SDK immediately, and do not utilize any package managers above, you can simply download our built `.Framework` file from out [GitHub Here](https://github.com/paypal/paypalcheckout-ios/raw/master/NativeCheckout.framework.zip) repository.

# 

## Integrating The Checkout Experience

We have tried to make integrating the PayPal Native Checkout experience into your app as painless and seamless as possible. There are multiple options available, so one can be used that best suits the application you are integrating with.

# 


### WebView Store

This scenario covers what might happen if your Application houses your Store inside a WebView. This assumes a simple setup, in which your application loads the store inside a `UIWebView`, and normally passes itself to `PayPal` and then back to your store in the `UIWebView`. 

After importing the PayPal Native Checkout SDK from one of the packaged options in the documentation before now, we will `initialize` the library, and add our merchant information to our P-List file.

# 


### Setting Up Deep Linking.
Setting up a Deep Link return, so that PayPal can let your app know, that payment has been completed or cancelled. Here is how we can add a `URI Scheme` for our application using `XCode` 7 and 8. 

You can modify and paste the `XML` structure below into your `Info.plist`

```xml
 <array>
	<dict>
		<key>CFBundleURLName</key>
		<string>com.merchant.webviewapp</string>
		<key>CFBundleURLSchemes</key>
		<array>
			<string>wvmerchant</string>
		</array>
	</dict>
 </array> 

```

This allows iOS to send Links and other URL requests that go to `wvmerchant://` in this example, to your app. 

If you would like to read more about `URI Schemes` and setting them up using the `XCode` interface, you can read more about it on [iDev101](http://www.idev101.com/code/Objective-C/custom_url_schemes.html).

### Setting up SDK Parameters
Now we need to add some values, that the PayPal Native Checkout SDK will use to facilitate the checkout process. You can copy and paste the two `XML` value pairs from the box below. Change the items in the `[]` square brackets, to represent the values you set prior to this step. 

```
<key>Paypal_Deep_Link</key>
<string>[URISCHEME]://paypal/authorize</string>
<key>Paypal_Client_Id</key>
<string>[YOUR CLIENT ID]</string>
```

That's it, we've got all the information for the SDK into the Appliation's `Info.plist`. All that is left is invoking the SDK.

# 

### Invoking the SDK 

As of PayPal Native Checkout SDK version `1.0.0` , there are two options available for integration. 

#### Quick/Auto Integration
In quick integration  mode, the SDK will wrap (swizzle) itself around your application. This allows you to focus on other things, and get the integration of PayPal out of the way. 

The PayPal Native Checkout SDK will add events to your `AppDelegate` if they are not defined, or it will extend the events that are currently available. In addition the PayPal Native Checkout SDK will add an additional `Delegate` to all of the `UIWebView` instances in your application. 

The SDK will listen for URL request to PayPal checkout, and intercept them. Instead offering a PayPal approved experience, and then returning the Customer back to your application.

To Invoke Auto Mode, importing the SDK header and Initializing the SDK from the `main.m` file is all that is needed. Here is an example of code you can add to your application's `main.m` file.

```c
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

// import the paypal native checkout sdk header file
#import <PayPalXO/PYPLCheckout.h>

int main(int argc, char * argv[]) {
    @autoreleasepool {

        // initalize the PayPal Checkout SDK, by passing
        // our AppDelegate class, so that the SDK can swizzle
        // or impliment missing methods.
        [PYPLCheckout initWithDelegate: [AppDelegate class]];

        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}

```

The PayPal  Native Checkout SDK needs to be Initialized before the `UIApplicationMain` is executed. It is during this routine, that Delegated System methods are wired. 

That's it, you are now ready to [Test]() the integration with your application. 


#### Manual Integration

Incase you didn't want to, or are having problem with the Quick/Auto method, you can integrate manually into your application using the three steps below.


#### 1. Add Application Level URI handling 

Add PayPal Native Checkout SDK the `application:openURL:` signature of your `AppDelegate.m` file. This is common amongst other SDKs like Facebook, and Twitter.
 
```c
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    return [[PYPLCheckout sharedInstance] openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
}
```

This gives an opportunity for the PayPal Native Checkout SDK to handle, the deep links you are registered in the prior section while editing the `Info.plist` file.

#### 2. Register the WebView with the SDK


Tell the PayPal Native Checkout SDK what `UIWebView` it should use.
   
```c
//ViewController.h
#import <PayPalXO/PYPLCheckout.h>

//ViewController.m
- (void)viewDidLoad {
    [super viewDidLoad];

    ...
    ...
    ...
    ...

    // init the paypal view inside the controller
    // that  needs native checkout
    [[PYPLCheckout sharedInstance] setWebView:(UIWebView*)self.viewWeb];
}
```
This sets up the sdk to be able to 
* Put the webview back in the merchant page once a purchase is complete
* Communicate from Paypal servers back into the iOS app.

#### 3. Add URL Intercept into the UIWebViewDelegate

The PayPal Native Checkout SDK needs to be setup to intercept URL requests that are headed to PayPal Checkout on the Web. When the SDK notices these URLs, it will stop the request, and show the approved PayPal Native checkout experience. 




```c
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest (NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    // Listen in on the webview for checkout urls
    // Returns a BOOL that can be used with other rules you may have here
    return [[PYPLCheckout sharedInstance] handleIfPPCheckout: (NSURLRequest *) request];
}

```

That's it, you are now ready to [Test]() the integration with your application. 


## Additional Questions, Answers, or just plain need help?

If you have more questions than answered in this guide, please head over to our [FAQ]() section. Hopefully the question you have has already been answered there. 

If you need more help, please reach out to our PayPal Native Checkout SDK team. You can do so by filing a support request on our [Github Page]().
