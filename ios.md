# Dragons Ahead

If you are looking to integrate with PayPal today, please see our Developer Documentation Portal for our Braintree SDK Express Checkout integration, or other options that maybe available to you.

https://developer.paypal.com/

#

# PayPal Native Checkout SDK for iOS 9 and 10

# 

The PayPal Native Checkout SDK for iOS, provides a simplified way for you to provide a secure and safe payment solution for your apps. 

* **Native Code Current Version** : 1.2.0
* **Experience Bundle Version** : 1.0.0
* **Experience** : Safari View Controller

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
    * [Swift](#swift-integration)
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
pod ‘paypal-nativecheckout’, :git => 'git@github.com:paypal/paypalcheckout-ios.git', :tag => '1.2.0'
```
# 

### Carthage 

Getting started with Carthage is easy, simply add our public `paypal-xo` repository to your Carthage file, and run `carthage update`. Our `.Framework` will be available for you to import into `XCode`.


```bash
github "paypal/paypalcheckout-ios" >= 1.2.0
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

### Invoking the SDK 

#### Swift Integration - Special Instructions

If you have a Swift application and would like to intergrate please follow these additional steps, otherwise you can move on to reviewing [Integration Steps](#manual-integration).

* Obtain a copy of the NativeCheckout.framework, [Direct Download of Release](https://github.com/paypal/paypalcheckout-ios/raw/master/NativeCheckout.framework.zip) 
* Copy unpack the archive, and copy `NativeCheckout.framework` to the root of your Swift project.
* Import the Framework dependency as an `Embedded Binary` in XCode using the General menu option in the Project Properties panes. 
* ![General](https://cloud.githubusercontent.com/assets/328000/20905298/0ae19eca-baf8-11e6-9a04-6014c389bc61.png)
* Create a `new` header file. (File > New File > Header File)
	* File Name PYPLCheckoutBridge.
	* [Example Header File](https://raw.githubusercontent.com/paypal/paypalcheckout-ios/master/PYPLCheckoutBridge.h) 
* Add a reference to the Bridging Header under Build Settings > Header Search Paths
* ![Build Phase Header Bridge](https://cloud.githubusercontent.com/assets/328000/20902889/046dbf92-baee-11e6-989a-8d843dff014c.png)
	* If the Framework was copied to the root of your project directory, the path to the header files would be `$(PROJECT_DIR)/NativeCheckout.framework/Headers`

PayPal NativeCheckout is now ready to be integrated with your application.


#### Integration Steps

You can integrate manually into your application using the three steps below.


#### 1. Add Application Level URI handling 

Add PayPal Native Checkout SDK the `application:openURL:` signature of your `AppDelegate` file. This is common amongst other SDKs like Facebook, and Twitter.
 
#### objective-c
```c
// AppDelegate.m
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    return [[PYPLCheckout sharedInstance] openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
}
```

#### swift 

```swift
// AppDelegate.swift
 func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
        
     let instance:PYPLCheckout = PYPLCheckout.sharedInstance() as! PYPLCheckout;
     let wasHandled:Bool = instance.openURL(application, open: url as URL, sourceApplication: sourceApplication as String!, annotation: annotation);
        
     return wasHandled
}
```

This gives an opportunity for the PayPal Native Checkout SDK to handle, the deep links you are registered in the prior section while editing the `Info.plist` file.

#### 2. Register the WebView with the SDK


Tell the PayPal Native Checkout SDK what `UIWebView` it should use.
 
#### objective-c 
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

#### swift
```swift
//ViewController.swift
override func viewDidLoad() {
    super.viewDidLoad()
        
    ...
    ...
    ...
    ...

    let instance:PYPLCheckout = PYPLCheckout.sharedInstance() as! PYPLCheckout;
    instance.setWebView(webV);
}
```

This sets up the sdk to be able to 
* Put the webview back in the merchant page once a purchase is complete
* Communicate from Paypal servers back into the iOS app.

#### 3. Add URL Intercept into the UIWebViewDelegate

The PayPal Native Checkout SDK needs to be setup to intercept URL requests that are headed to PayPal Checkout on the Web. When the SDK notices these URLs, it will stop the request, and show the approved PayPal Native checkout experience. 

#### objective-c

```c
// ViewController.m
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest (NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    // Listen in on the webview for checkout urls
    // Returns a BOOL that can be used with other rules you may have here
    return [[PYPLCheckout sharedInstance] handleIfPPCheckout: (NSURLRequest *) request];
}

```

#### swift

```swift
//ViewController.swift
func webView(_ webView: UIWebView, shouldStartLoadWith request: URLRequest, navigationType: UIWebViewNavigationType) -> Bool {
     let instance:PYPLCheckout = PYPLCheckout.sharedInstance() as! PYPLCheckout;
     let wasHandled:Bool = instance.handleIfPPCheckout(request as URLRequest!);
     return wasHandled
}
```

That's it, you are now ready to [Test]() the integration with your application. 

## Sample App

You can find a sample application in Swift with the iOS SDK integration [here](https://github.com/paypal/paypalcheckout-ios-sample). You will need a Sandbox Paypal account which you can create at the [Paypal developer portal](https://developer.paypal.com/)

## Additional Questions, Answers, or just plain need help?

If you have more questions than answered in this guide, please head over to our [FAQ](faq.md) section. Hopefully the question you have has already been answered there. 

If you need more help, please reach out to our PayPal Native Checkout SDK team. You can do so by filing a support request on our [Github Page](https://github.com/paypal/paypalcheckout-ios).
