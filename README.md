# Native checkout integration for Webview merchants 

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
* Create a merchant app on our developer portal (since we will need the client_id when invoking the Paypal SDK)
    * Go to https://developer.paypal.com/developer/applications/
    ![Developer portal](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step1.png)
    * Add a name  and click on `create app` 
    ![Create App](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step2.png)
    * You will see the Merchant client_id generated in the next page. 
    ![Add app](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step3.png)
* Setup a deep link url for allowing Paypal to pass back and forth data to our server side while making a Paypal payment. [Here is how to set it up.](http://www.idev101.com/code/Objective-C/custom_url_schemes.html)

### 3-step Code Change:

#### 1. In your App delegate:
```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation

{
    return [paypal_xo openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
    
}
```

Or if you are already handling deep links in your app, give an opportunity for `paypal-xo` sdk to handle, for the deep link you are registering specifically for native checkout related handling (as called out in the previous section).

#### 2. In the view controller which owns the webview 
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

#### 3. In the webview delegate

Add the following (or do appropriately per your app's setup). This lets the Paypal sdk intercept the webview and put up the  native-checkout experience.

```
- (BOOL)webView:(UIWebView *)webView
shouldStartLoadWithRequest:(NSURLRequest *)request
 navigationType:(UIWebViewNavigationType)navigationType {

    //Listen in on the webview for checkout urls
    return [paypal_xo handleCheckoutUrl: (NSURLRequest *) request];
}

```

Or if you are already handling `shouldStartLoadWithRequest` for your webview, give an opportunity to the PaypalSDK to check for handling specific URLs too. We only capture hermes checkout urls to be able to put up the native checkout experience.

## Do you have a sample app that I can checkout the integration on ?

https://github.paypal.com/nativexo/webview-sample

## Feedback / Questions

Please feel free to reach out to DL-PP-nativexo@paypal.com





