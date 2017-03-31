# Dragons Ahead

If you are looking to integrate with PayPal today, please see our Developer Documentation Portal for our Braintree SDK Express Checkout integration, or other options that maybe available to you.

https://developer.paypal.com/

#

# PayPal Native Checkout SDK for Android API Level 19+ (4.4.4 KitKat)

# 

The PayPal Native Checkout SDK for Android, provides a simplified way for you to provide a secure and safe payment solution for your apps. 

* **Native Code Current Version** : 1.1.0
* **Experience Bundle Version** : 1.1.0
* **Experience** : Chrome Custom Tab (or) Default browser on your device.

For detailed version history please see our [Change Log](changelog.md).

# 

### Table of Contents

* [Getting Started](#getting-started)
* [Obtaining a PayPal Client ID](#paypal-client-id)
    * [Direct Download](#direct-download)
* [Integrating the SDK](#integrating-the-checkout-experience)
    * [**Integration Type**: WebView Store](#webview-store)
        * [Setting Up Deep Linking and Settings](#setting-up-deep-linking-and-sdk-setup)
* [Invoking  the PayPal Native Checkout SDK](#invoking-the-sdk)
    * [Integration](#manual-integration)

# 

## Getting Started


The PayPal Native SDK is available at [github path here].
This SDK ships with React Native. If you are currently using React Native in your application, it will likely collide. Please reach out to us, we'd love to work with you while we sandbox React Native away from the SDK.

# 

### PayPal Client ID

Before you start, you should have already obtained a PayPal Client ID from the PayPal Developer portal. If you haven't done that step yet, please stop here, and follow the directions in our [Obtaining a PayPal Client ID](how_to_merchantid.md) docs.

# 

### Direct Download

You can obtain an copy of the AAR from our [GitHub Repo](https://github.com/paypal/paypalcheckout-android/blob/master/paypal-xo-android.aar?raw=true). To use with your project, using Android Studio follow these directions:

File > New Module

![File New Module](https://cloud.githubusercontent.com/assets/328000/21118959/1aeb4e9c-c075-11e6-8d12-f1edf39fcb54.png)

Select Import AAR 


![Import AAR](https://cloud.githubusercontent.com/assets/328000/21118985/39c9102e-c075-11e6-8de3-7385d94abe1f.png)

Select the AAR file you downloaded earlier. Android Studio will create a new module in your project, and populate the gradle file with a reference to the AAR.

### Adding React-Native to your project.

The PayPal Native Checkout SDK relies on Facebook's React Native to add React Native to your project use the following steps from the Project root.

```bash
$ npm install --save react-native@0.32
```

Not using npm? No problem. You can download the compressed react-native module from [here](https://github.com/paypal/paypalcheckout-android/blob/master/react-native.tar.gz)

In your app's build.gradle file add the React Native, and required dependencies:

```
dependencies {
    ...
    compile 'com.facebook.react:react-native:0.32'
    compile 'com.android.support:appcompat-v7:23.4.0'
    compile 'com.android.support:customtabs:23.3.0+'
    compile 'com.google.android.gms:play-services-appindexing:8.4.0'
}
```

In your project's build.gradle file add an entry for the local React Native maven directory:
```
allprojects {
    repositories {
        ...
        maven {
            // All of React Native (JS, Android binaries) is installed from npm
            // If you used Git instead of NPM, change this path
            url "$rootDir/react-native/android"
        }
    }
    ...
}
```



## Integrating The Checkout Experience

We have tried to make integrating the PayPal Native Checkout experience into your app as painless and seamless as possible. There are multiple options available, so one can be used that best suits the application you are integrating with.

# 


### WebView Store

This scenario covers what might happen if your Application houses your Store inside a WebView. This assumes a simple setup, in which your application loads the store inside a `WebView`, and normally passes itself to `paypalxo` and then back to your store in the `WebView`. 

After importing the PayPal Native Checkout SDK from one of the packaged options in the documentation before now, we will setup the paypal checkout library.

# 


### Setting Up Deep Linking and SDK Setup

The PayPal Native Checkout SDK for Android has Deep Linking Intents setup in the Library's Manifest file. To complete deeplinking you should provide the SDK with a unique scheme for your app and provide us your ClientID. 

To accomplish this, we add values to the `res/values/strings.xml` of the application.


```xml
<string name="paypalxo_deeplink_scheme">merchantsample</string>
<string name="paypalxo_client_id">[Your Client ID]</string>
```

If you would like to read more about `URI Schemes` and setting them up using the `Android Studio` interface, you can read more about it on [iDev101](https://developer.android.com/training/basics/intents/filters.html).

# 

### Invoking the SDK 

As of PayPal Native Checkout SDK version `1.0.0` :

In the activity that has the `WebView` that you want to be intercepted,

```java
import com.paypal.paypal_xo_android.paypalxo;

...
...
...
...

import android.webkit.WebView;
import android.webkit.WebViewClient;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        WebView webview = (WebView) findViewById(R.id.webview);

        ...
        ...
        ...
        ...


        // Setup PayPalXO SDK to be able to handle URLs from your webview
        final AppCompatActivity selfActivity = this;
        webview.setWebViewClient(new WebViewClient() {
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
               final Uri uri = Uri.parse(url);
               return paypalxo.getInstance().handleIfPaypalXO(url, this.activity);

            }

            public void onLoadResource(WebView view, String url) {
               paypalxo.getInstance().setupxojs();
            }
        });

        // set webview and deep link for paypal handling in paypalxo SDK
        paypalxo.getInstance().setIntegration(paypalxo.IntegrationType.WEBVIEW, webview,this).setDeepLink(res.getString(R.string.paypalxo_deep_link));
    }
}


```

## Sample App
You can find a sample application with the android SDK integration [here](https://github.com/pvenkatakrishnan/merchant-android). You will need a Sandbox Paypal account which you can create at the [Paypal developer portal](https://developer.paypal.com/)

## Additional Questions, Answers, or just plain need help?

If you have more questions than answered in this guide, please head over to our [FAQ](faq.md) section. Hopefully the question you have has already been answered there. 

If you need more help, please reach out to our PayPal Native Checkout SDK team. You can do so by filing a support request on our [Github Page](https://github.com/paypal/paypalcheckout-android).

