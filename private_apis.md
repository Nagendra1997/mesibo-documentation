

## Private Backend APIs

mesibo provides you helper backend APIs for administrative tasks such as creating users, groups, etc. These are private backend APIs and are simply an added utility. It is only meant for use in the demo apps.  The following are some of the functionality provided by private backend APIs

### User Login and Authentication

When a new user signs-up up with your services, your app will communicate with your private APIs for the login and verification. Your private APIs verifies the new sign-in (say, using username/password, or OTP, etc.). If successful, it saves the user in a database and then registers this user with mesibo using mesibo backend API. mesibo returns a token for this use which you can give it to your app so that your app can communicate using mesibo real-time API.

![mesibo login](login_token.png)

Your goal is to create a mesibo user and generate an access token for a valid user. The way you determine whether a user is valid or not, how you implement the authentication step is up to you. 

For example, in the demo applications, mesibo provides a simple Login API to authenticate users with an OTP sent to their mobile number. This is provided only to test the demo app. If you wish, you can replace it with any other login implementation or authentication method which uses your own SMS based OTP service or an email-based OTP or anything of your choice.

Here is the Login REST API used in the demo app.

This will inform the backend to send an OTP to the phone number the user signs up with.
```
https://app.mesibo.com/conf/api.php? op=login&phone=USER_PHONE_TO_SEND_OTP&name=USER_NAME&appid=MESIBO_APPLICATION_ID
```

This will verify the OTP entered in the next step if it matches the one sent in the previous sign-up step. If OTP matches then authentication is successful and in the response of this request you will get the access token generated for that user.
```
https://app.mesibo.com/conf/api.php?op=login&phonel=USER_PHONE&name=USER_NAME&appid=MESIBO_APPLICATION_ID&code=OTP_RECEIVED_ON_PHONE
```
Note that you only need to make these calls if when are using mesibo’s backend when testing the demo apps. For your own application, you will have your own backend and you need to replace this with your own authentication APIs. 


### Contact Synchronization
In the case of Whatsapp and other messaging apps, the displayed contact name is customized to the one you have saved in your contacts. You can get to know the people who are already using the app and chat with them. 

To do this you need to synchronize the contacts with the mesibo backend which has a list of all users who are using the demo app. First, you need to obtain the list of contacts stored on your device and validate them. Next you send the list of contacts to the backend and mark them as synchronized. This is convenient because in the future when you need to sync again you only need to send the contacts which are not previously synced up.


For contact synchronization in the demo app, mesibo provides the following private API
```
https://app.mesibo.com/conf/api.php?op=getcontacts&token=xxxxxxxx&ts=LAST_SYNC_TIME &phones=LIST_OF_PHONE_NUMBERS
``` 

In the response of `getcontacts` request you will get:
1. A list of your contacts who are using the demo app
2. The latest status and profile pictures of each of these contacts, that they are using on the demo app. 
3. Your own profile picture and status in the demo app

Now, the backend/host will carry out the synchronization process and store the results in the database. You can take a look at the mesibo messenger backend database schema and use it as a reference. Your backend also needs to inform the appropriate users who are having the synced user in their contact list. This way your users will get to know who is already using your app when they signup and also be notified when a new person (who is already in your contacts) joins the app.

On the demo app, when any new user/contact joins the app you will be notified through `Mesibo_onMessage`









