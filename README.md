# **README.md**

# **React Native SDK Integration**

## **Download React Native Freshchat SDK:** 

1. Our node package name is ‘react-native-freshchat-sdk’.
2. In your react native project’s Package.json file, under dependencies add
    ”react-native-freshchat-sdk”: “{{latest-version}}”
3. After installing sdk, run `react-native link` command
4. For latest SDK version please refer the following link: 
    [https://www.npmjs.com/package/react-native-freshchat-sdk](https://www.npmjs.com/package/react-native-freshchat-sdk)

    Note: Please refer the change logs before updating to newer version of the SDK.

## **iOS Setup:**

1. Go to ios folder of your react native project.
2. Add an entry for FreshchatSDK as shown below in Podfile,
     ```
    target 'ProjectName' do
    pod 'FreshchatSDK', :path=> '../node_modules/react-native-freshchat-sdk/ios/FreshchatSDK.podspec'
    end
    ```
3. Run pod install from ios directory

## **Android Setup:**

1. Add maven { url "https://jitpack.io" } to your project level build.gradle file.
    ```
    allprojects {
        repositories {
            .....
            maven { url "https://jitpack.io" }
        }
    }
    ```
## **Steps to integrate Freshchat React Native SDK:**

### **1. Initialisation:**

Freshchat SDK can be initialised when the app gets started. You can use the below snippet to initialise SDK.
```
import { Freshchat, FreshchatConfig } from 'react-native-freshchat-sdk';
var freshchatConfig = new FreshchatConfig(APP_ID, APP_KEY);
Freshchat.init(freshchatConfig);
```
Where APP_ID and APP_KEY are your account’s specific which can be found in
Freshchat portal under Settings -> Mobile SDK.

### **2. Set User Properties:**

**setUser** API is used to update user details. You can use the below snippet to update user details.
  
```
import { FreshchatUser } from 'react-native-freshchat-sdk';
var freshchatUser = new FreshchatUser();
freshchatUser.firstName = "John";
freshchatUser.lastName = "Doe";
freshchatUser.email = "johndoe@dead.man";
freshchatUser.phoneCountryCode = "+91";
freshchatUser.phone = "1234234123";
Freshchat.setUser(freshchatUser, (error) => {
    console.log(error);
});
```

### **3. Set User Custom Properties:**

You can set or update custom properties of user using setUserProperties API.
You can set up to 126 unique properties per account.

```
var userPropertiesJson = {
    "user_type": "Paid",
    "plan": "Gold"
}
Freshchat.setUserProperties(userPropertiesJson, (error) => {
    console.log(error);
});
```

### **4. Identify and restore user:**

**identifyUser** API is used to identify and restore the user’s conversations.

**External Id** - This should (ideally) be a unique identifier for the user from your system
like a user id or email id etc and is set using the identifyUserWithExternalID: API

**Note:** This cannot be changed once set for the user

**Restore Id** - This is generated by Freshchat for the current user, given an external id
was set  and can be retrieved anytime using the [FreshchatUser
sharedInstance].restoreID API. 

**Note:** Restore Id should be stored at your backend and you can implement logic to 
retrieve them to restore conversations back.

```
Freshchat.identifyUser("EXTERNAL_ID", "RESTORE_ID", (error) => {
    console.log(error);
});
```

**Note:** For first time user, you can pass restoreId as null. SDK will generate restoreId for the user.
If you are listening to the below event, you will be notified when restoreId is generated.

```
Freshchat.addEventListener(
    Freshchat.EVENT_USER_RESTORE_ID_GENERATED,
    () => {
        console.log("onRestoreIdUpdated triggered");
        Freshchat.getUser((user) => {
            var restoreId = user.restoreId;
            var externalId = user.externalId;
            console.log("externalId: " + externalId);
            console.log("restoreId: " + restoreId);
        })
    }
);
```

When this event is triggered, you can get current user details and access restoreId from there. Store this identifier in your backend. So when the same user logs back from different device call identifyUser method with the EXTERNAL_ID and RESTORE_ID of the corresponding user.

**Note:** Restore Id generation is an asynchronous process. You need to listen to this event throughout your application lifecycle. Usually generation happens after user initiates a conversation.

### **5. Show Conversations:**

Show conversations will display all channels published publicly.
To show conversations call the following API,

```
Freshchat.showConversations();
```

If there is only a single [channel](https://support.freshchat.com/support/solutions/articles/232815), this API will directly open that channel. If there are multiple channels, this API will open the list of channels

### **6. Show Conversations with options:**

**showConversations** API will display channel list with all channel by default. If you want to open a particular channel directly or list a small section of channels, you can use **showConversations(conversationOptions)** API.
This API accepts conversationOptions where you can specify what channels to open.

```
import { ConversationOptions } from 'react-native-freshchat-sdk';
var conversationOptions = new ConversationOptions();
conversationOptions.tags = ["premium"];
conversationOptions.filteredViewTitle = "Premium Support"; 
Freshchat.showConversations(conversationOptions);
```

[Tags](https://support.freshchat.com/support/solutions/articles/232815) are used to filter channels belongs to the tag. 
FilteredViewTitle is the title given to channels list page when there are multiple channel matches found.

**Note:** If there is no matching channel found for the option applied, SDK will take you to the conversation page of the [default](https://support.freshchat.com/support/solutions/articles/232815) channel.

### **7. Show FAQs:**

Show Faq’s helps you to display FAQs in your application
To show FAQs call the following method,

```
Freshchat.showFAQs();
```

### **8. Show FAQs with options:**

**showFAQs** API will display all categories and articles by default. If you want to open a particular category/article directly or list a small section of  categories/articles, you can use **showFAQs(faqOptions)** API.

```
import { FaqOptions } from 'react-native-freshchat-sdk';
var faqOptions = new FaqOptions();
faqOptions.tags = ["premium"];
faqOptions.filteredViewTitle = "Tags";
faqOptions.filterType = FaqOptions.FilterType.ARTICLE;
Freshchat.showFAQs(faqOptions);
```

**FilteredViewTitle** - title given to the result list page of the filter applied.
**Tags** - name of the tags based on which filter gets applied.
**FilterType** - Category/Article.
**Category** - combination of articles and sub categories.
**Article** - contains only articles.

**Note:** When there is no match on the filter applied, it will by default displays all FAQ categories.

### **9. Reset User:**

Reset user clears the current user details.
When user logs out, you can call the below API to reset Freshchat user.

```
Freshchat.resetUser();
```

This API will clears user related info from the application.

### **10. Get User:**

User object will give details about the current user using the chat.
To get the user details use the following method,

```
Freshchat.getUser((user) => {
    console.log(user);
})
```

User object has following details,
firstName, lastName, email, restoreId, externalId, phoneNumber and phoneCountryCode.

### **11. Dismiss Freshchat:**

On any situation where all screens needs to be cleared, this helps in clearing Freshchat screens.
You can use dismissFreshchatViews API to close all freshchat screens.

```
Freshchat.dismissFreshchatViews();
```

**Note:** In Android, Only the Freshchat screens that are present in the top of the stack will be dismissed.

### **12. Send Message:**

Instead of going to Freshchat screen and user initiating a chat, this will help send message instantly from anywhere in the application.
To send a message on behalf of a user call the following method,

```
import { FreshchatMessage } from 'react-native-freshchat-sdk';
var freshchatMessage = new FreshchatMessage();
freshchatMessage.tag = "channel-tag";
freshchatMessage.message = "text message";
Freshchat.sendMessage(freshchatMessage);
```

Construct FreshchatMessage with tag and message properties. 
Pass it to sendMessage method. 
Tag - tag of the channel to which message needs to be sent.
Message - actual message to be passed to the channel.

**Note:** When there are multiple matching channels, channel which was
created first will get the message. If there is no matching channel found, message will 
be sent to the default channel. 

### **13. Unread message count:**

This gives the count on number of messages left uread by users.
To get unread count immediately add the following code,

```
Freshchat.getUnreadCountAsync((data) => {
    console.log(data);
});
```

**Note:** This works best with Push notification integration.

## **EVENTS:**

Some of the actions performed in SDK are exposed by SDK. You can listener to them by adding a listener.

### **1. Adding listener:**

To add callback for any event use the following approach,

```
Freshchat.addEventListener(
    EVENT_NAME, // Name of the event to be listened
    eventHandler
);

const eventHandler = (actionData) => {
    // Handling the data inside callback
};
```

### **2. Supported Events:**

#### **Unread message count listener:**

When user's unread message count changes, this event will be triggered. When this is triggered you can call getUnreadCountAsync API to get the number of unread messages.

```
Freshchat.addEventListener(
    Freshchat.EVENT_UNREAD_MESSAGE_COUNT_CHANGED,
    () => {
        console.log("onUnreadMessageCountChanged triggered");
        Freshchat.getUnreadCountAsync((data) => {
            var count = data.count;
            var status = data.status;

            if (status) {
                console.log("Message count: " + count);
            } else {
                console.log("getUnreadCountAsync unsuccessful");
            }
        });
    }
);
```

**Note:** This works best with Push notification integration.

#### **Freshchat User interaction Event listener:**

Whenever user interacts inside any Freshchat screen this event gets triggered.

```
Freshchat.addEventListener(
    Freshchat.EVENT_USER_INTERACTED,
    (actionData) => {
        console.log("FRESHCHAT_EVENTS triggered");
        var action = actionData.user_action;
    }
);
```

#### **Override link handling:**

This listener will override URL click handling functionality. Once overridden, app needs to handle the link. The callback gives the URL link clicked by the user.

```
Freshchat.addEventListener(
    Freshchat.EVENT_EXTERNAL_LINK_CLICKED,
    (data) => {
        console.log("link - ", data.url);
    }
);
```

### **3. Removing listeners:**

To remove any listener from getting callback add the following code,

```
Freshchat.removeEventListeners(EVENT_NAME);    
```

## **Push notification integration:**
### **1. Server side integration:**
#### **Android:**
In Firebase Console Under your project go to cloud messaging tab and you will see your Server Key, copy that. If you do not have a Firebase account for your application. Refer [https://firebase.google.com/docs/android/setup](https://firebase.google.com/docs/android/setup)

Save this FCM server key in Freshchat web portal under Settings -> Mobile SDK

#### **iOS:**
Refer [here](https://firebase.google.com/docs/android/setup) for generating .p12 file and uploading it to Freshchat.

### **2. Client side integration:**
Please follow the below steps to integration push notifications in you mobile app. 
Below steps are under the assumption that your app is handing other push notifications. 

**Steps:**
* Saving push token: Each application will have a push token. Freshchat needs this token to send push notification to user. You can use below snippet to send token to Freshchat SDK.
    Freshchat.setPushRegistrationToken(token);
* Passing received push notification payload to Freshchat SDK.
    ```
    Freshchat.isFreshchatNotification(notification, (freshchatNotification) => {
        if (freshchatNotification) {
            Freshchat.handlePushNotification(notification);
        } else {
            // handle your app notification
        }
    })
    ```

### **3. Set freshchat notification config (Android only)**

Add the below code in index.js
```
var freshchatNotificationConfig = new FreshchatNotificationConfig();
freshchatNotificationConfig.priority = FreshchatNotificationConfig.NotificationPriority.PRIORITY_HIGH;
freshchatNotificationConfig.notificationSoundEnabled = false;
Freshchat.setNotificationConfig(freshchatNotificationConfig);
```

## **Push Notification checklists:**

We have identified some potential areas where problem happens. So we consolidated those into checklists, check if you have done everything.

### **iOS:**

1. Make sure you have uploaded correct push certificate in Freschat portal. 
2. Make sure that you are checking in development or production environment and upload the respective certificate in Freschat portal.
3. Don’t check in iOS simulator, since it did not have push notification capability. Check in real device.
4. Push notification capability should be enabled in your Xcode project.
5. Make sure you gave permission to push notification for your application. If not go to settings -> under your application, enable push notification.
6. Confirm you are passing on device token to the Freshchat using the following method,
    ```
    Freshchat.setPushRegistrationToken(device.pushToken);
    ```
7. Under Appdelegate class make sure you have either UNUserNotificationCenter delegate methods or [UIApplicationDelegate application:didReceiveRemoteNotification:fetchCompletionHandler:] is implemented. 
    And if UNUserNotificationCenter framework is being used, make sure delegate has been set for that.
8. Add respective react bridging codes on the push notification delegate methods.
9. Refer the codes here for plugin implementation, confirm you have implemented all the recommendations.
10. If you have Notification extension in your app, make sure its not blocking the notification.


### **ANDROID:**

1. Make sure you have added correct FCM key of your application in Freshchat portal
2. Don’t check in emulator, which don’t have google play service enabled. Check in real device.
3. Confirm you are passing on device token to the Freshchat using the following method,
    ```
    Freshchat.setPushRegistrationToken(device.pushToken);
    ```
4. Make sure you are receiving notification from FCM.
5. Refer the codes here for plugin implementation, confirm you have implemented all the recommendations.


If still problem persists, check out the following URL,

**https://web.freshchat.com/app/api/notif_debug?convId=CONV_ID** where CONV_ID is the conversation id of the conversation for which you did not receive notification. CONV_ID is the last numeric part of the url when you open the conversation in the browser. Please make sure you are logged in as ADMIN when opening this url. It will give you the reason behind the push notification failure.


## For More References

More instructions here: [Freshchat for Android](https://support.freshchat.com/support/solutions/articles/229319)

More instructions here: [Freshchat for iOS](https://support.freshchat.com/support/solutions/articles/232945)
