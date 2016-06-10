# Native checkout integration (iOS only) for Webview merchants 

This integration sets up end-to-end native checkout experience on the Sandbox environment for en-US locale.

![Sequence](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/Native-Sequence.png)

## Demo on Sandbox with a Bigcommerce sample merchant using the webview intercept model

https://paypal.box.com/s/yhcjwh9xaxvaxo9hi177ki0vg18ebjfe

## What do I need to do to get this great experience in my App ?

### App Setup Instructions:

* In you iOS application, add the following to your `Podfile`
```

target '<fill-in-your-app-target>' do
pod 'paypal-xo', :git => 'git@github.paypal.com:nativexo/paypal-xo-ios.git'

pod 'React', :git => 'git@github.com:facebook/react-native.git', :tag => 'v0.19.0', :subspecs => [
'Core',
'RCTText',
'RCTWebSocket',
'RCTImage',
'RCTNetwork',
'RCTActionSheet',
'RCTLinkingIOS',
'RCTImage'
]
end
```
* Do `pod install`
* Create a merchant app on our developer portal (since we will need the client_id when invoking the Paypal SDK).
    * Go to https://developer.paypal.com/developer/applications/
    ![Developer portal](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step1.png)
    * Add a name  and click on `create app` 
    ![Create App](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step2.png)
    * You will see the Merchant client_id generated in the next page. 
    ![Add app](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step3.png)
* Setup deep linking using Universal Links. If you donot have that setup on your app yet, use url schemes. This will be used for allowing Paypal to pass back and forth data to our server side. [Here is how to set it up.](http://www.idev101.com/code/Objective-C/custom_url_schemes.html)

NOTE: The preferred way is to use Universal Links. If you have this setup already please let us know as we donot have a sample app with Universal links and have tested it only with url schemes.

### 3-step Code Change:

#### 1. In your App delegate:
```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation

{
    return [paypal_xo openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
    
}
```

Or if you are already handling deep links in your app, give an opportunity for `paypal-xo` sdk to handle, for the deep link you are registering specifically for native checkout related handling (as called out in the previous section).

#### 2. In the view controller which owns the webview: 
Initialize the paypal sdk by passing:
   * *webview*: reference to the webview in your view controller
   * *merchant_client_Id*: the merchant client id from the developer portal for the app you created
   * *deep link url*: The url scheme/universal link that you created for the sdk to talk back and forth to the native backend
   
   
```
//ViewController.h
#import "paypal/PaypalSetup.h"

//ViewController.m
- (void)viewDidLoad {
    [super viewDidLoad];

    ...
    ...
    ....
    ...

    // init the paypal view inside the controller
    // that  needs native checkout
       [paypal_xo init: _viewWeb
               url:@"<your_uri_scheme_here>" //example: wvmerchant://paypal/authorize
               merchantId:@"<your_app_client_id>"]; //example: AfI9K9SL-OMYew-EBUzO8ExuWp6FM8d-v-iRdQXDL-JnsotNF88Jf8tepB6rOtNnHUjtM2Tzz70xLnd
    
}
```
This sets up the sdk to be able to 
* put the webview back in the merchant page once a purchase is complete
* communicate from Paypal servers back into the iOS app.

#### 3. In the webview delegate:

Add the following (or do appropriately per your app's prior setup). This lets the `paypal-xo` sdk intercept the webview and put up the  native-checkout experience.

```
- (BOOL)webView:(UIWebView *)webView
shouldStartLoadWithRequest:(NSURLRequest *)request
 navigationType:(UIWebViewNavigationType)navigationType {

    //Listen in on the webview for checkout urls
    return [paypal_xo handleCheckoutUrl: (NSURLRequest *) request];
}

```

Or if you are already handling `shouldStartLoadWithRequest` for your webview, give an opportunity to the `paypal-xo` to check for handling specific URLs too. We only capture and act on hermes checkout urls to intercept and put up the native checkout experience.

## Do you have a sample app that I can checkout the integration on ?

https://github.paypal.com/nativexo/webview-sample

## How do I test it ?
The experience around auth and consent is not yet built. That is independent of the integration steps. This whole experience expects oneTouch cookie to be present in the browser. In order to see it working end to end, on your simulator or phone:

* Go to https://www.sandbox.paypal.com/signin/inject-iframe-test
* Login with your sandbox user credentials and enable `Stay logged in for faster checkout	`
* Now launch your merchant app, add something to your cart and click on the Paypal checkout button. You must see the native checkout experince. 
* Click on `Pay Now` button and you should see the payment go through successfully and the webview should load the merchant's redirect url.


## Feedback / Questions

Please feel free to reach out to DL-PP-nativexo@paypal.com





