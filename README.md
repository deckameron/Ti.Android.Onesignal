# Titanium Android OneSignal

This module gives you the possibility to integrate OneSignal into you're Appcelerator Android app. It's even possible to target people by registering tags, send InApp Messages, register a external user id, prompt the user for location permission and a bit more.

## Generate Credentials

Before setting up the Titanium SDK, you must generate the appropriate credentials for the platform(s) you are releasing on:

- ANDROID - [Generate a Google Server API Key](https://documentation.onesignal.com/docs/generate-a-google-server-api-key)

## Follow Guide

### Setup

1. Integrate the module into the `modules` folder and define them into the `tiapp.xml` file:

    ```xml
    <modules>
      <module platform="iphone">com.williamrijksen.onesignal</module>
      <module platform="android">com.williamrijksen.onesignal</module>
    </modules>
    ```
1. Configure your app into the App Settings panel for the right Platform (Android and/or iOS).
1. To use OneSignal on iOS devices, register the OneSignal-appId into  `tiapp.xml`:

    ```xml
    <property name="OneSignal_AppID" type="string">[App-id]</property>
    ```
1. To use OneSignal on Android devices, register some meta-data as well:

    ```xml
    <meta-data android:name="onesignal_app_id"
                   android:value="[App-id]" />
    ```

### Usage

#### Push Notification
1. Register device for Push Notifications

   ```js
   // This registers your device automatically into OneSignal
   var onesignal = require('ti.android.onesignal');
   ```
2. You can request permission to use notifications:
   ```js
   onesignal.promptForPushNotificationsWithUserResponse(function(obj) {
       alert(JSON.stringify(obj));
   });
   ```
   
3. SetLocationShared
Disable or enable location collection (defaults to enabled if your app has location permission).
   ```js
	onesignal.setLocationShared(false);
   ```
   
5. To add the possibility to target people for notifications, send a tag:

   ```js
	onesignal.sendTag({ key: 'foo', value: 'bar' });
   ```
6. Delete tag:

   ```js
   onesignal.deleteTag({ key: 'foo' });
   ```
7. Get tags:

    ```js
   onesignal.getTags(function(e) {
        if (!e.success) {
            Ti.API.error("Error: " + e.error);
            return
        }

        Ti.API.info(Ti.Platform.osname === "iphone"? e.results : JSON.parse(e.results));
    });
    ```
8. SetExternalUserId:
If your system assigns unique identifiers to users, it can be annoying to have to also remember their OneSignal user ID's as well. To make things easier, OneSignal now allows you to set an external_id for your users. Simply call this method, pass in your custom user ID (as a string), and from now on when you send a push notification, you can use include_external_user_ids instead of include_player_ids.
	```js
   onesignal.setExternalUserId("user_153321568");
   ```
9. RemoveExternalUserId:
If your user logs out of your app and you would like to disassociate their custom user ID from your system with their OneSignal user ID, you will want to call this method.
	```js
   onesignal.removeExternalUserId();
   ```
10. SetSubscription:
You can call this method with false to opt users out of receiving all notifications through OneSignal. You can pass true later to opt users back into notifications.
	```js
	onesignal.setSubscription(true);
	```

11. IdsAvailable:

    ```js
    onesignal.idsAvailable(function(e) {
        //pushToken will be nil if the user did not accept push notifications
        alert(e);
    });
    ```

12. Opened listener:
   The returned content is matching the available payload on OneSignal:
   - [Android](https://documentation.onesignal.com/docs/android-native-sdk#section--osnotificationpayload-)

	    ```js
	    onesignal.addEventListener('notificationOpened', function (evt) {
	        alert(evt);
	        if (evt) {
	            var title = '';
	            var content = '';
	            var data = {};

	            if (evt.title) {
	                title = evt.title;
	            }

	            if (evt.body) {
	                content = evt.body;
	            }

	            if (evt.additionalData) {
	                data = evt.additionalData;
	            }
	        }
	        alert("Notification opened! title: " + title + ', content: ' + content + ', data: ' + evt.additionalData);
	    });
	    ```

13. Received listener:
    The returned content is matching the available payload on OneSignal:
   - [Android](https://documentation.onesignal.com/docs/android-native-sdk#section--osnotificationpayload-)

	   ```js
	   onesignal.addEventListener('notificationReceived', function(evt) {
	       console.log(' ***** Received! ' + JSON.stringify(evt));
	   });
	   ```
14. Subscription Changed listener:
    The  `onOSSubscriptionChanged`  method will be fired on the passed-in object when a notification subscription property changes.
	This includes the following events:
	-   Getting a Registration Id (push token) from Google
	-   Getting a player / user id from OneSignal
	-   `OneSignal.setSubscription`  is called
	-   User disables or enables notifications
	```js
	onesignal.addEventListener('subscriptionChanged', function(evt) {
		Titanium.API.info(' OneSignal ***** subscriptionChanged!' + JSON.stringify(evt));
		//var canNotify = evt.to.subscribed;
	});
	   ```

#### InApp Messages
1. Sending InApp Messages:
In-App Messages are highly customizable pop-up modals that any mobile app user (subscribed or unsubscribed) can receive when they are inside your app. The link below will teach you how to use it.

- [https://documentation.onesignal.com/docs/sending-in-app-messages](https://documentation.onesignal.com/docs/sending-in-app-messages)

2. InApp Message Clicked listener:
    The returned payload is available on OneSignal:
   - [Payload](https://documentation.onesignal.com/docs/android-native-sdk#section--osinappmessageaction-)

   ```js
   onesignal.addEventListener('inAppMessageClicked', function(evt) {
	   /*
	   evt.clickName 		//An optional click name defined for the action element. `null` if not set
	   evt.clickUrl		//An optional URL that opens when the action takes place. `null` if not set.
	   evt.firstClick		//`true` if this is the first time the user has pressed  any action on the In App Message.
	   evt.closesMessage	//Did the action close the In App Message. `true` the In App Message will animate off the screen. `false` the In App Message will stay on screen until the user dismisses.
	   */
       console.log(' ***** InApp Clicked! ' + JSON.stringify(evt));
   });
   ```

3. Add Trigger:
Add a trigger, may show an In-App Message if its triggers conditions were met.
	```js
	OneSignal.addTrigger("level", 5);
	 ```
4. Add Triggers:
Add a object of triggers, may show an In-App Message if its triggers conditions were met.
	 ```js
	OneSignal.addTriggers(
		{"level", 5},
		{"page", 8},
		{"tutorial", 11}
	);
	```
5. Remove Triggers for key:
Removes a single trigger for the given key, may show an In-App Message if its triggers conditions were met.
	```js
	OneSignal.removeTriggerForKey("level");
	```
6. Remove Triggers for keys:
Removes a list of triggers based on a collection of keys, may show an In-App Message if its triggers conditions were met.
	```js
	OneSignal.removeTriggerForKeys(["level","page","tutorial"]);
	```
7. Remove Triggers for keys:
Gets a trigger value for a provided trigger key.
	```js
	var trigger = OneSignal.getTriggerValueForKey("level");
	console.log(trigger.object);
	//Returns a single trigger value for the given key, if it exists, otherwise returns `null`
	```
8. Pause InApp Messages:
Allows you to temporarily pause all In App Messages. You may want to do this while the user is watching a video playing a match in your game to make sure they don't get interrupted at a bad time.
	```js
	OneSignal.pauseInAppMessages(true);
	```
9. Enable Vibrate:
By default OneSignal always vibrates the device when a notification is displayed unless the device is in a total silent mode. Passing false means that the device will only vibrate lightly when the device is in it's vibrate only mode.
	_You can link this action to a UI button to give your user a vibration option for your notifications._  
	```js
	OneSignal.enableVibrate(true);
	```
10. Enable Sound:
By default OneSignal plays the system's default notification sound when the device's notification system volume is turned on. Passing false means that the device will only vibrate unless the device is set to a total silent mode.
	_You can link this action to a UI button to give your user a different sound option for your notifications._
	```js
	OneSignal.enableSound(true);
	```
#### Email
1. Setting the user email:
`setEmail` allows you to set the user's email address with the OneSignal SDK.
	```js
	OneSignal.setEmail("example@domain.com");
	```
2. Email Update Success Event
	```js
	onesignal.addEventListener('emailUpdatedSuccess',  function(evt)  { 
		console.log(evt.success);
		console.log(evt.email);
	});
	```
3. Email Update Error Event
	```js
	onesignal.addEventListener('emailUpdatedError',  function(evt)  { 
		console.log(evt.error);
		console.log(evt.message);
		console.log(evt.email);
	});
	```
4. logoutEmail
	If your app implements logout functionality, you can call `logoutEmail` to dissociate the email from the device:
	```js
	OneSignal.logoutEmail();
	```
5. Email Subscription Changed Event
Tracks changes to email subscriptions (ie. the user sets their email or logs out). In order to subscribe to email subscription changes you can implement the following:
	```js
	onesignal.addEventListener('emailSubscriptionChanged',  function(evt)  { 
		console.log(evt);
	});
	```
Cheers!

## Build yourself

1. `brew install yarn --without-node` to install yarn without relying on a specific Node version
1. In the root directory execute `yarn install`
1. Step into the android directory
1. Copy `build.properties.dist` to `build.properties` and edit to match your environment
1. To build the module execute `rm -rf build && mkdir -p build/docs && ../node_modules/.bin/ti build -p android --build-only`

#### Google Play Services

Since Titanium 7.x this module relies on [https://github.com/appcelerator-modules/ti.playservices](ti.playservices)

If you still need to support Titanium 6.x and you need to change the used Google Play Services version, execute the following actions:
1. Install the Google Play Services on your system:

   ```bash
   sdkmanager "extras;google;m2repository"
   ```
1. Fetch the 4 needed *.aar files from the SDK path `extras/google/m2repository/com/google/android/gms`
   - base
   - basement
   - gcm
   - idd
   - location

   For the version you want use.
1. Extract the *.aar file, and rename the `classes.jar` to `google-play-services-<part>.jar`.
1. Update the used jars in the `lib` folder.
1. Update the res folder with the one from the `google-play-services-basement.jar`
