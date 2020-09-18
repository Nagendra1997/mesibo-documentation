# Integrating Mesibo with React-Native

[Mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high-quality voice chat between users
  - Adding real-time video calling in your apps

[React Native](https://facebook.github.io/react-native/) uses React to build native apps.
React Native is a framework developed by Facebook and it's like react, but it uses native components instead of web components as building blocks. React native is a way to develop mobile apps using React and JavaScript.

    
Mesibo provides real-time APIs for messaging, voice, and video calls which can be easily integrated into any application on Android or iOS. Irrespective of the application platform that you are on, Mesibo can be easily integrated into any application as Mesibo provides platform-specific/Native SDK.

In this section, we will learn how to integrate React-Native with Mesibo. In the sample app provided, the user interface is developed using React-Native which interacts with Mesibo to send messages and make audio/video calls.

### Prerequisites

- Read the [Get Started Guide](/documentation/tutorials/get-started/)

- [Write your First mesibo Enabled Application](/documentation/tutorials/get-started/first-app/)

- Basic knowledge of creating applications in React-Native

- Building and using Native Modules for [Android](https://reactnative.dev/docs/native-modules-android) and [iOS](https://reactnative.dev/docs/native-modules-ios) in React-Native

### Set up React-Native dependencies and the directory structure.

Refer to [React-Native documentation](https://facebook.github.io/react-native/docs/getting-started) for installing the required dependencies and setting up your development environment.

### Integrating Mesibo with React-Native 

1. Create a new react-native project and initialize it. 
```
react-native init FirstApp 
```
This will provide you `App.js` where you need to develop your UI component in Javascript and the `Android` directory where you develop your Android Application, and `ios` directory where you develop your iOS application.

In the steps below, we will describe integrating Mesibo with React-Native as a Native Module. The steps will have examples from Android. You can follow similar steps for iOS. 

2. Add Mesibo SDK 

   - Add Mesibo SDK to your Android host project by adding Gradle dependency and performing Gradle sync as explained in our [First Android App tutorial](https://mesibo.com/documentation/tutorials/first-app/android/)
   - Import mesibo API and add mesibo initialization code in your onCreate method

```java
import com.mesibo.api.mesibo;
```
 
3.Create a native module that acts as a bridge between Mesibo and React-Native. 

For example , 
```javascript
import { NativeModules } from "react-native";
var MesiboModule = NativeModules.MesiboModule;
```

Follow the steps below to create `MesiboModule`- a Native Mesibo Module(Android)

3a. Create a class `MesiboModule` which extends `ReactContextBaseJavaModule`. Here you can implement Mesibo API functions and listeners.
```java
public class MesiboModule extends ReactContextBaseJavaModule implements Mesibo.MessageListener, Mesibo.ConnectionListener, MesiboCall.MesiboCallListener{
    
    
    public MesiboModule(@Nonnull ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Nonnull
    @Override
    public String getName() {
        return null;
    }
}
```
You need to add methods and listeners to the above class which includes the API functions of Mesibo. 

These `ReactMethod` functions can be called from `App.js`
For example,
```java
    @ReactMethod
    public void onSendMessage(final String mMessage) {
            Mesibo.MessageParams p = new Mesibo.MessageParams();
            p.peer = mRemoteUser.address;
            p.flag = Mesibo.FLAG_READRECEIPT | Mesibo.FLAG_DELIVERYRECEIPT;

            if(mMessage.isEmpty()){
                    return;
            }

            Mesibo.sendMessage(p, Mesibo.random(), mMessage.toString().trim());
    }


    @ReactMethod
    public void onLaunchMessagingUi() {
            MesiboUI.launchMessageView(getCurrentActivity(), mRemoteUser.address, 0);
    }

    @ReactMethod
    public void onAudioCall() {
            MesiboCall.getInstance().call(getReactApplicationContext(), 0, mProfile, false);
    }

    @ReactMethod
    public void onVideoCall() {
            MesiboCall.getInstance().call(getReactApplicationContext(), 0, mProfile, true);
    }

```
Listeners initialized here will be called by Mesibo.
```java

    @Override
    public void Mesibo_onConnectionStatus(int status) {
            Log.d(LOG_TAG, "Connection Status: "+ status);
            sendReactEvent("connectionStatus", ""+ status);
    }

    @Override
    public boolean Mesibo_onMessage(Mesibo.MessageParams messageParams, byte[] data) {
            try {
                    String message = new String(data, "UTF-8");
                    Toast toast = Toast.makeText(getReactApplicationContext(),
                            "Message: " + message,
                            Toast.LENGTH_SHORT);
                    toast.show();
                    sendReactEvent("message", message);

            } catch (Exception e) {
            }

            return true;
    }


   @Override
    public void Mesibo_onMessageStatus(Mesibo.MessageParams messageParams) {
            try {
                sendReactEvent("messageStatus", ""+messageParams.getStatus());
            } catch (Exception e) {
            }
    }

```
Initialize Mesibo as follows. 

```java
    private void mesiboInit(final DemoUser user, final DemoUser remoteUser) {
            Mesibo api = Mesibo.getInstance();
            api.init(getReactApplicationContext());

            Mesibo.addListener(this);
            Mesibo.setSecureConnection(true);
            Mesibo.setAccessToken(user.token);
            Mesibo.setDatabase("mydb", 0);
            Mesibo.start();
	    
  	    //.. Refer Source Code ..//
}
                                     
```

3b. Create a class `MesiboPackage` that implements `ReactPackage` and the Native Module.
```java
public class MesiboPackage implements ReactPackage {

  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }

  @Override
  public List<NativeModule> createNativeModules(
                              ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();

    modules.add(new MesiboModule(reactContext));

    return modules;
  }

}
```

3c. Modify `MainApplication.java` 
```java
public class MainApplication extends Application implements ReactApplication {

  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new MesiboPackage()
          );
    }

    @Override
    protected String getJSMainModuleName() {
      return "index";
    }
  };

  @Override
  public ReactNativeHost getReactNativeHost() {
    return mReactNativeHost;
  }

  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
  }
}
```

Override method `getPackages` in `MainApplication` class, to return `MesiboPackages` to include Mesibo packages along with the react packages.
```java
    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new MesiboPackage()
          );
    }
```
The Android Module setup is now complete.

4. Now,edit `App.js` to add UI components and call Mesibo Methods by importing `MesiboModule`
```javascript
import { NativeModules } from "react-native";
var MesiboModule = NativeModules.MesiboModule;
```

Add users like below. See [Creating Users and Endpoints](https://mesibo.com/documentation/tutorials/get-started/first-app/#create-users-endpoints) to learn about creating users for your app.

```javascript
var mUser1 = {
  token: "6f75363d479e4fccad30b8654d9e20e523c4b68abef2784d08e6615e759",
  name: "User-1",
  address: "react_user_1",
};
var mUser2 = {
  token: "d5249c06b88c6df88e6903ec7fe2b5eb8debd2a19df323fc3e9915e757",
  name: "User-2",
  address: "react_user_2",
};

MesiboModule.setUser1(mUser1.token, mUser1.name, mUser1.address);
MesiboModule.setUser2(mUser2.token, mUser2.name, mUser2.address);
```
Create UI components and then bind UI events with Mesibo Methods. For example, you can create a button to send a message. 
```javascript
<Button
	onPress={() => {
                console.log(this.state.mText);
                MesiboModule.onSendMessage(this.state.mText);
                this.textInput.clear();
        }}
        title="SEND"
/>
```
Similarly, you can create and link other UI components to log in users, make calls, etc. 
```javascript
        <View style={{ flexDirection: "row" }}>
          <View style={styles.buttonStyle}>
            <Button
              onPress={() => {
                console.log("Connect User1");
                MesiboModule.onLoginUser1();
              }}
              title="LOGIN AS USER1"
            />
          </View>
          <View style={styles.buttonStyle}>
            <Button
              onPress={() => {
                console.log("Connect User2");
                MesiboModule.onLoginUser2();
              }}
              title="LOGIN AS USER2"
            />
          </View>
        </View>

        <View style={styles.buttonContainer}>
          <Button
            onPress={() => {
              MesiboModule.onAudioCall();
            }}
            title="AUDIO CALL"
          />
        </View>

        <View style={styles.buttonContainer}>
          <Button
            onPress={() => {
              MesiboModule.onVideoCall();
            }}
            title="VIDEO CALL"
          />
        </View>
````

5. Start the React Native server and run your native app.
Go to the project folder, open terminal, and run the react-native server which will load files from your project directory.
```
react-native start
```
To run your Android Application,
```
react-native run-android
```
That's it! You have successfully integrated Mesibo with React-Native.

