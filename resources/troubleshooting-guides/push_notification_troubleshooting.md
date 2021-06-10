# Campaign push notifications

To set up server key/APNS server certificate in the Campaign instance, complete the following steps:

{% tabs %}
{% tab title="Android" %}
## Set up a server key

1. To configure the app in Google Firebase, access [Firebase Console](https://console.firebase.google.com/) and sign in using your Google Developer credentials. 
2. Add a project and add your app under that project using the application ID that you used in the app.
3. Add the generated `google-service.json` to the project and set up the project and module level gradle files.

    For more information on configuring your application in Firebase, please read the [add Firebase to your Android project tutorial](https://firebase.google.com/docs/android/setup#console).

4. Verify that Firebase server key is configured in the Campaign instance by clicking **Adobe Campaign &gt; Administrator &gt; Channels &gt; Mobile App (AEP SDK)**.
5. Select the mobile app from the list and verify that the push channel settings status for Android is `Android key set up success`.

   ![Server key configuration in Android](../../.gitbook/assets/android_server_key.png)
{% endtab %}

{% tab title="iOS" %}
## Set up the APNS server certificate

1. Add push notification entitlement to your app.

   For more information, please read the [enable the push notifications capability tutorial](https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns?language=objc).

2. To configure the APNS certificate, register the app on your Apple developer account and generate the certificate.

   For more information, please read the [establishing a certificate-based connection to APNs tutorial](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_certificate-based_connection_to_apns?language=objc).

3. Verify that the APNS server certificate is configured in the Campaign instance by clicking **Adobe Campaign &gt; Administrator &gt; Channels &gt; Mobile App (AEP SDK)**.
4. Select the mobile app from the list and verify that push channel settings status for iOS is `iOS certificate with filename \*.pem is successfully uploaded`.
5. Verify that you are using production certificate for production environment and development certificate for development environment. ![Configured push certificate in Campaign](../../.gitbook/assets/campaign_ios_cert_configured.png)
{% endtab %}
{% endtabs %}

For more information about configuration, please read the documentation on [channel specific application configurations in Adobe Campaign](https://helpx.adobe.com/campaign/kb/configuring-app-sdk.html#ChannelspecificapplicationconfigurationinAdobeCampaign).

## Set up an Android or iOS app to receive push notifications

{% tabs %}
{% tab title="Android" %}
### Set up the Android app

1. Create the Firebase Messaging Service and add it to the Android Manifest file.

   For more information, please read the [set up a Firebase Cloud Messaging client app on Android tutorial](https://firebase.google.com/docs/cloud-messaging/android/client).

2. Generate a push token for the app using the Firebase instance ID class.
3. In the `onComplete` function of `OnCompleteListener`, set the push identifier by calling the `MobileCore.setPushIdentifier` API.

   For more information, please read the setPushIdentifier section of the [Adobe Analytics mobile services guide](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/adobe-analytics-mobile-services#set-up-push-messaging).

### Verify that the push token has successfully synced with the Experience Cloud ID service

{% hint style="warning" %}
If SDK privacy status is `optedout`, the push identifier will not be set.
{% endhint %}

1. Verify the push token sync with the Experience Cloud ID service (ECID).  
2. To verify, launch your app connected to a Project Griffon session.   
3. In the list of events, verify that you have an event with type `SetPushIdentifier`.
4. In the details panel on the right, verify the value of the push token for this device. The value in `pushIdentifier` is the same value that is sent to the ECID service. ![Verify SetPushIdentifier event](../../.gitbook/assets/push_token_to_identity.png)
5. To verify that app’s push token is mapped to the correct Experience Cloud ID(ECID) in the Campaign instance, click **Adobe Campaign &gt; Administrator &gt; Channels &gt; Mobile App (AEP SDK)**.
6. Select your app, and under the mobile application subscribers, verify that the Experience Cloud ID and the registration token for the user is displayed. ![App subscriber list, verify mid and push token](../../.gitbook/assets/subscriber_list_android.png)
7. If you are using Charles, verify that the push token has successfully synced with the ECID service.
8. Check for the `demdex` request, which is marked with the red line, as seen in the following screenshot.  
   ![ECID network request for push token sync](../../.gitbook/assets/push_identifier.png)
9. Verify the successful response (HTTP status 200) for this network call.

{% endtab %}

{% tab title="iOS" %}
### Set up the iOS app

1. After you launch the app, call `registerForRemoteNotifications` to register the app and receive the APNS token.

   This API generates and returns the APNS token through the `application:didRegisterForRemoteNotificationsWithDeviceToken:` delegate function.

   For more information, please read the [registering your app with APNs tutorial](https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns?language=objc)

2. In `application:didRegisterForRemoteNotificationsWithDeviceToken:`, call `setPushIdentifier` to set the push identifier.

   For more information, see the setPushIdentifier of the [Adobe Analytics mobile services reference](https://aep-sdks.gitbook.io/docs/using-mobile-extensions/adobe-analytics-mobile-services#set-up-push-messaging).

### Verify that the push token has successfully synced with the Experience Cloud ID service

{% hint style="warning" %}
If SDK privacy status is `optedout`, the push identifier will not be set.
{% endhint %}

1. Verify the push token sync with the Experience Cloud ID service (ECID).  
2. To verify, launch your app connected to a Project Griffon session.   
3. In the list of events, verify that you have an event with type `SetPushIdentifier`.
4. In the details panel on the right, verify the value of the push token for this device.

   The value in pushIdentifier is the same value that is sent to the ECID service. ![Verify SetPushIdentifier event](../../.gitbook/assets/push_token_to_identity.png)

5. To verify that app’s push token is mapped to the correct Experience Cloud ID(ECID) in the Campaign instance, click **Adobe Campaign &gt; Administrator &gt; Channels &gt; Mobile App (AEP SDK)**.
6. Select your app, under the mobile application subscribers verify that the Experience Cloud ID and the Registration token for the user is displayed. ![App subscriber list, verify mid and Registration token](../../.gitbook/assets/subscriber_list_ios.png)
7. If you are using Charles, verify that the push token has successfully synced with the ECID service.
8. Check for the **demdex request**, which is marked with the red line in the following screenshot.
   ![ECID network request for push token sync](../../.gitbook/assets/charles_demdex_call_ios.png)  
9.  Verify the successful response (HTTP status 200) for this network call.
{% endtab %}
{% endtabs %}

## Troubleshooting push notification events tracking

The following events are related to the tracked push notifications:

* Impression (Notification is delivered)
* Click (User clicked the notification)
* Open (App opened in response to user's click on the notification)

  For more information about implementing the tracking of push notification events, see [Push Tracking](https://helpx.adobe.com/campaign/kb/push-tracking.html).

### Troubleshooting using Project Griffon

You can verify the push notification tracking in Project Griffon.

1. Connect your app to a Griffon session.  
2. Send a push notification to app from your Campaign instance.

   To know more about how to send push notification, please read the [preparing and sending a push notification tutorial](https://docs.adobe.com/content/help/en/campaign-standard/using/communication-channels/push-notifications/preparing-and-sending-a-push-notification.html).

3. Receive a push notification in the device.
4. Click on the push notification to launch the app.  
5. In the list of events, verify that you have an event with type `CollectData`.  
6. In the Details panel on the right, verify the value of the action.  
   The value of the action should be 7 for impression, 2 for the click, and 1 for open.

   Impression event tracking (action value 7)

   ![Shows Push notification impression tracking](../../.gitbook/assets/push_tracking_impression.png)

    Click event tracking (action value 2)

    ![Shows Push notification click tracking](../../.gitbook/assets/push_tracking_click.png)

    Open event tracking (action value 1)

    ![Shows Push notification open tracking](../../.gitbook/assets/push_tracking_open.png)

### Troubleshooting using Charles

To verify that a successful network call is made to track the push notification interaction event, look for the GET request with the query string `id=broadlogID,deliveryID,action` to the Campaign server. 

For additional details, please see screenshots below:

Impression event tracking (action value 7)

![Impression event tracking for notification](../../.gitbook/assets/tracking_impression.png)

Click event tracking (action value 2)

![Click event tracking for notification](../../.gitbook/assets/tracking_click.png)

Open event tracking (action value 1)

![Open event tracking for notification](../../.gitbook/assets/tracking_open.png)

