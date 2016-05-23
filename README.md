# Nativexo-Integration- WIKI

## Demo on Sandbox with a Bigcommerce sample merchant using the webview intercept model

https://paypal.box.com/s/yhcjwh9xaxvaxo9hi177ki0vg18ebjfe

## What do I need to do to get this great experience in my App ?

### App Setup Instructions:

* Pull down the native-xo ios static library from here
* Setup a deep link url for allowing Paypal to pass back and forth data to our server side while making a Paypal payment
* Run `pod install` in your app

### Code Changes:

* In your App delegate add the following
```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation

{
    return [PaypalSetup openURL: (UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation];
    
}
```

Or if you are already handling deep links in your app, give an opportunity for the Paypal SDK to handle it once you are done with your App deep link handling checks

* In the view controller which owns the webview add the following:
```

```

* In your webview's handler add the following (or do appropriately per your apps need, just let the Paypal sdk handle the flow at some point before you return from the handler)

## Where can I pull the latest react-native bundle ?

For now, it is hosted at http://paypalmerchant.herokuapp.com/reactbundle

## Do you have a sample app that I can checkout the integration on ?

https://github.paypal.com/nativexo/nativexo-webview-sample

## Feedback / Questions

Please feel free to reach out to DL-PP-nativexo@paypal.com





