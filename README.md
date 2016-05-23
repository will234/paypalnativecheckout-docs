# Nativexo-Integration- WIKI

## Demo on Sandbox with a Bigcommerce sample merchant using the webview intercept model

https://paypal.box.com/s/yhcjwh9xaxvaxo9hi177ki0vg18ebjfe

## What do I need to do to get this great experience in my App ?

### App Setup Instructions:

* Pull down the native-xo ios static library from here
* Create a merchant app on our developer portal (since we will need the client_id when invoking the Paypal SDK)
    * Go to https://developer.paypal.com/developer/applications/
    ![Developer portal](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step1.png)
    * Add a name  and click on `create app` 
    ![Create App](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step2.png)
    * You will see the Merchant client_id generated in the next page. 
    ![Add app](https://github.paypal.com/nativexo/nativexo-integration-Wiki/blob/master/step3.png)
* Setup a deep link url for allowing Paypal to pass back and forth data to our server side while making a Paypal payment. [Here is how to set it up.](http://www.idev101.com/code/Objective-C/custom_url_schemes.html)
* Run `pod install` in your app

### Code Changes:

* In your App delegate add the following
```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation

{
    return [PaypalSetup openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
    
}
```

Or if you are already handling deep links in your app, give an opportunity for the Paypal SDK to handle it for the deep link you are registering for Paypal nativexo related handling.

* In the view controller which owns the webview add the following:
```
//ViewController.h
#import "paypal/PaypalSetup.h"

//ViewController.m
- (void)viewDidLoad {
    [super viewDidLoad];

    NSString *fullURL = @"https://your-merchant-site.com";
    NSURL *url = [NSURL URLWithString:fullURL];
    NSURLRequest *requestObj = [NSURLRequest requestWithURL:url];
    [_viewWeb loadRequest:requestObj];
    _viewWeb.delegate = self;

    // init the paypal view inside the controller
    // that  needs native checkout
    [PaypalSetup init: _viewWeb];
    
}
```

* In your webview's handler add the following (or do appropriately per your apps need, just let the Paypal sdk handle the flow at some point before you return from the handler)

```
- (BOOL)webView:(UIWebView *)webView
shouldStartLoadWithRequest:(NSURLRequest *)request
 navigationType:(UIWebViewNavigationType)navigationType {

    //Listen in on the webview for checkout urls
    return [PaypalSetup handleCheckoutUrl: (NSURLRequest *) request];
}

```

Or if you are already handling `shouldStartLoadWithRequest` for your webview, give an opportunity to the PaypalSDK to check for handling specific URLs too. We only capture hermes checkout urls to be able to put up the native checkout experience.


## Where can I pull the latest react-native bundle ?

For now, it is hosted at http://paypalmerchant.herokuapp.com/reactbundle

## Do you have a sample app that I can checkout the integration on ?

https://github.paypal.com/nativexo/nativexo-webview-sample

## Feedback / Questions

Please feel free to reach out to DL-PP-nativexo@paypal.com





