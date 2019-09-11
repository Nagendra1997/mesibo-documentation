# Integrating Mesibo with React-Native

[Mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps

[React Native](https://facebook.github.io/react-native/) uses React to build native apps.
React native is a framework developed by Facebook and it's like react, but it uses native components instead of web components as building blocks. React native is a way to develop mobile apps using React and JavaScript.

    
Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS . Irrespective of the application platform that you are on, Mesibo can be easily integrated into any application as Mesibo provides platatform specific/Native SDK .

In this section, we will learn how to integrate React-Native with Mesibo . In the sample app provided, the user interface is developed using React-Native which interacts with Mesibo to send messages and make audio/video calls .

### Prerequisites

- Read the [Preparation Guide](/documentation/tutorials/first-app/)

- Read the [Anatomy of a Mesibo Application](/documentation/tutorials/first-app/anatomy/) 

- Basic knowledge of creating Android applications in React-Native


## Integrating Mesibo with React-Native

Here’s what you need to do to integrate React Native components with Mesibo:

### Set up React Native dependencies and the directory structure.

You will need Node, the React Native command line interface, a JDK, and Android Studio.
Refer to [React-Native documentation](https://facebook.github.io/react-native/docs/getting-started) for installing the required dependencies and setting up the directory structure.

### Develop your React Native components

1. Create a new react-native project and initialise it. 
```
react-native init ProjectMesibo
```
This will provide you `App.js` where you need to develop your UI component in Javascript and the `Android` folder where you develop your Android Application.

2.Create a native module that acts as a bridge between Mesibo and React-Native. For example , `MesiboModule.js`

```javascript
import {NativeModules} from 'react-native';
module.exports = NativeModules.MesiboModule;

```

3. Add Mesibo SDK in the Android part

From Android Studio load ProjectMesibo by clicking on android folder.

   - Add Mesibo SDK to your Android host project by adding gradle dependency and performing gradle sync as explained in our [First Android App tutorial] (https://mesibo.com/documentation/tutorials/first-app/android/)
   - Import mesibo API and add mesibo initialization code in your onCreate method

```java
import com.mesibo.api.mesibo;
```
 Let's get into coding.
 
3a. Create a class `MesiboModule` which extends `ReactContextBaseJavaModule`.
```java
public class MesiboModule extends ReactContextBaseJavaModule {
    
    
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
You can add methods to the above class which includes the API functions of Mesibo.
For example,
```java
 @ReactMethod
    public void init(final String token) {
    // To initialise and start mesibo

        getCurrentActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Mesibo api = Mesibo.getInstance();

                api.init(getReactApplicationContext());

                MesiboCall mCall = MesiboCall.getInstance();
                mCall.init(getReactApplicationContext());

                if (0 != api.setAccessToken(token)) {
                    Log.d(TAG, "bad token: ");
                    return;
                }

                api.setDatabase("messaging.db", 0);
                api.start();
            }
        });
    }


@ReactMethod
    public void sendMessage(String to, String message) {
    // To send Messages
	  Mesibo.MessageParams p = new Mesibo.MessageParams();
	  p.peer = to;
	  Mesibo.sendMessage(p, Mesibo.random(), message);

    }

    
    @ReactMethod
    public void makeAudioCall(String to) {
        // To make audio call
        Mesibo.MessageParams messageParams = new Mesibo.MessageParams(to, 0, Mesibo.FLAG_DEFAULT, 0);
        MesiboCall.getInstance().call(getReactApplicationContext(), Mesibo.random(), messageParams.profile, false);
    }

```
3b. Create a class `MesiboPackage` that implements `ReactPackage`
```java
public class MainApplication extends Application implements ReactApplication {

  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      @SuppressWarnings("UnnecessaryLocalVariable")
      List<ReactPackage> packages = new PackageList(this).getPackages();
      // Packages that cannot be autolinked yet can be added manually here, for example:
      // packages.add(new MyReactNativePackage());
      return packages;
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
The Android Part setup is now complete.

4. Now,edit `App.js` to add UI components and call Mesibo Methods by importing `MesiboModule.js`
```javascript
import MesiboModule from './MesiboModule';

```
Initialise Mesibo like below
```javascript

// Refer to https://mesibo.com/documentation/ to generate token
const mesiboToken = "4ef59c6683d45c2f0c7f43fa894dd0f77dacf61cd6d98eff6651a";
const mesiboDestination = "TestUser";


MesiboModule.init(mesiboToken);
```
Create UI componenets and then bind UI events with Mesibo Methods . For example , you can create a button to send a Message . 
```javascript
<View style={styles.buttonContainer}>
	<Button
  		onPress={() => {
		MesiboModule.sendMessage(mesiboDestination);
	        }}
  	   	title="Launch UI" 
	/>
</View>
```
Similarly you can create and link other UI components to interact with Mesibo.

4. Start the React Native server and run your native app.
Go to the project folder, open terminal and run the react-native server which will load files from your project directory.
```
sudo react-native start
```
To run your Android Application,
```
sudo react-native run android
```
That's it! You have sucessfully integrated Mesibo with React-Native.

