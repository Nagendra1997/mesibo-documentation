# Integrating Flutter with Mesibo 

[Mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps
  
[Flutter](https://flutter.io/) by Google is a new framework that allows us to build beautiful native Apps on iOS and Android from a single codebase. It provides Fast Development, Expressive and Flexible UI, and Native Performance.

Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS . Irrespective of the application platform that you are on, Mesibo can be easily integrated into any application as Mesibo provides platatform specific/Native SDK .

In this section, we will learn how to integrate flutter with Mesibo . In the sample app provided, the user interface is developed using flutter which interacts with Mesibo to send messages and make audio/video calls via method channels.

### Prerequisites

- Read the [Preparation Guide](/documentation/tutorials/first-app/)

- Read the [Anatomy of a Mesibo Application](/documentation/tutorials/first-app/anatomy/) 

- Basic knowledge of creating Android applications in Flutter 


### How to use method Channels to integrate Mesibo with Flutter? 

- The Flutter portion of the app sends commands to  its host to perform actions.Here ,the host is Mesibo which controls ,the iOS or Android portion of the app, over a platform channel.For example ,to send a message you just need to enter the access token for your application and the destination user address.

- Mesibo listens on the platform channel, and recieves the information about the action to be performed. In the case of sending a message, it will recieve a "Send Message" command from flutter ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Flutter portion of the app.

For more information refer to [Flutter Method Channels](https://flutter.dev/docs/development/platform-integration/platform-channels)

Follow the steps below to integrate Flutter with Mesibo:

### 1. Install Android Studio

Android Studio offers a complete, integrated IDE experience for Flutter.

   - Android Studio, version 3.0 or later

   
 ### 2. Add Mesibo Sdk
 From Android Studio Inside this project open the Android host portion of your Flutter app :

   - Add Mesibo SDK to your Android host project by adding gradle dependency and performing gradle sync as explained in our [First Android App tutorial] (https://mesibo.com/documentation/tutorials/first-app/android/)
   - Import mesibo API and add mesibo initialization code in your onCreate method

```java
import com.mesibo.api.mesibo;
```
 Let's get into coding.
 
 ### 3a. Create the Flutter platform client
 
Create Method channels to connect flutter UI and Mesibo inside `main.dart`
 
 First, construct the channel. Use a MethodChannel with a single platform method that returns the `message` from Mesibo
 ```java
 class _HomeWidgetState extends State<HomeWidget> {
  static const platform = const MethodChannel("mesibo.flutter.io/messaging");
  static const EventChannel eventChannel = EventChannel('mesibo.flutter.io/mesiboEvents');
  
//Get Message from Host(Mesibo)
  
 ```
Next, invoke the Mesibo API method that you need on the method channel. For example, in the sample App we will be using the following API methods ,which will be called from the Method channel.
```java

 void _setCredentials() async {
    print("Set Credentials clicked");
    //get AccessToken and Destination From TextField and add it in a list then send it to native mesibo activity where these can be used to start mesibo
    final List list = new List();
    list.add(userAccessTokenController.text);
    list.add(destinationController.text);

    await platform.invokeMethod("setAccessToken", {"Credentials": list});
  }
  
  Future<void> _sendMessage() async {
    print("Send Message clicked");
    await platform.invokeMethod('sendMessage');
  }

  void _launchMesiboUI() async {
    print("LaunchMesibo clicked");
    await platform.invokeMethod("launchMesiboUI");
  }

  void _makeAudioCall() async {
    print("AudioCall clicked");
    await platform.invokeMethod("audioCall");
  }

  void _makeVideoCall() async {
    print("VideoCall clicked");
    await platform.invokeMethod("videoCall");
  }
```
Finally, replace the build method from the template to contain a small user interface that displays the buttons which perform the required functionality. In the Sample App we have,
```java
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      crossAxisAlignment: CrossAxisAlignment.center,
      children: <Widget>[
        InfoTitle(),
        accessTokenLayoutWidget(userAccessTokenController),
        destinationLayoutWidget(destinationController),
        RaisedButton(
          child: Text("Set Credentials"),
          onPressed: _setCredentials,
          textTheme: ButtonTextTheme.accent,
        ),
        RaisedButton(
          child: Text("Send Message"),
          onPressed: _sendMessage,
          textTheme: ButtonTextTheme.accent,
        ),
        RaisedButton(
          child: Text("Make Audio Call"),
          onPressed: _makeAudioCall,
          textTheme: ButtonTextTheme.accent,
        ),
        RaisedButton(
          child: Text("Make Video Call"),
          onPressed: _makeVideoCall,
          textTheme: ButtonTextTheme.accent,
        ),
        RaisedButton(
          child: Text("Launch Mesibo UI"),
          onPressed: _launchMesiboUI,
          textTheme: ButtonTextTheme.accent,
        ),
        Text(_mesiboStatus),
      ],
    );
  }

```
 ### 3b. Add an Android platform-specific implementation 
 Start by opening the Android host portion of your Flutter app in Android Studio:

- Start Android Studio
- Select the menu item File > Open…
- Navigate to the directory holding your Flutter app, and select the android folder inside it. Click OK.
- Open the MainActivity.java file located in the java folder in the Project view.

Extend your MainActivity Class to implement `Mesibo.ConnectionListener,` and `Mesibo.MessageListener``

Next, create a MethodChannel and set a MethodCallHandler inside the onCreate() method. Make sure to use the same channel name as was used on the Flutter client side. Call your API methods in this Method Channel.


 ```java
public class MainActivity extends FlutterActivity implements  Mesibo.ConnectionListener, Mesibo.MessageListener, Mesibo.MessageFilter {
    private static final String MESIBO_MESSAGING_CHANNEL = "mesibo.flutter.io/messaging";
    private static final String MESIBO_ACTIVITY_CHANNEL = "mesibo.flutter.io/mesiboEvents";
    private static final String MesiboErrorMessage = "Mesibo has not started yet, Check Credentials";
    private static MesiboCall mCall = null;
    EventSink mEventsSink;
    private static String mUserAccessToken;
    private static String mPeer;
    Mesibo.MessageParams mParameter;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        GeneratedPluginRegistrant.registerWith(this);
       
        new EventChannel(getFlutterView(), MESIBO_ACTIVITY_CHANNEL).setStreamHandler(
                new StreamHandler() {
                
                Mesibo.ConnectionListener messageListener;

                    @Override
                    public void onListen(Object arguments, EventSink events) {
                        mEventsSink = events;
                    }

                    @Override
                    public void onCancel(Object arguments) {
                    }
                }
        );


        new MethodChannel(getFlutterView(), MESIBO_MESSAGING_CHANNEL).setMethodCallHandler(
                new MethodCallHandler() {
                    @Override
                    public void onMethodCall(MethodCall call, Result result) {

                        if(call.method.equals("setAccessToken")){
                            //get credentials from flutter
                            ArrayList credentials = call.argument("Credentials");
                            if (credentials != null) {
                                mUserAccessToken = credentials.get(0).toString();
                                mPeer = credentials.get(1).toString();

                                //start mesibo here
                                mesiboInit();

                                // set Mesibo MessageParams
                                mParameter = new Mesibo.MessageParams(mPeer, 0, Mesibo.FLAG_DEFAULT, 0);

                            }

                        }else if (call.method.equals("sendMessage")) {

                            if(null != mPeer) {
                                //send message to desired user added in mParameter
                                Mesibo.sendMessage(mParameter, Mesibo.random(), "Hello from Mesibo Flutter");
                                mEventsSink.success("Message Sent to "+mPeer);
                            }else{
                                mEventsSink.success(MesiboErrorMessage);
                            }

                        } else if (call.method.equals("launchMesiboUI")) {
                            if(null != mPeer) {
                                //Launch Mesibo Custom UI
                                MesiboUI.launchMessageView(MainActivity.this, mParameter.peer, 0);
                            }else{
                                mEventsSink.success(MesiboErrorMessage);
                            }
                        } else if (call.method.equals("audioCall")) {
                            if(null != mPeer) {
                                //make audio call
                                MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(), mParameter.profile, false);
                            }else{
                                mEventsSink.success(MesiboErrorMessage);
                            }
                        } else if (call.method.equals("videoCall")) {
                            if(null != mPeer) {
                                //make Video Call
                                MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(), mParameter.profile, true);
                            }else{
                                mEventsSink.success(MesiboErrorMessage);
                            }
                        } else {
                            result.notImplemented();
                        }
                    }
                }
        );
    }


    private void mesiboInit() {

        Mesibo mesibo = Mesibo.getInstance();
        mesibo.init(this);
        mCall = MesiboCall.getInstance();
        mCall.init(this);
        Mesibo.addListener(this);
        Mesibo.setAccessToken(mUserAccessToken);
        Mesibo.setPath(Environment.getExternalStorageDirectory().getAbsolutePath());
        Mesibo.setDatabase("myAppDb.db", 0);
        Mesibo.start();

    }
}
 ```
 ### Run the application
From Android Studio, run your application by connecting a mobile device/Emulator. 
Now, In the Sample Android application 
1. Enter your `AUTH_TOKEN` (which can be obtained from Mesibo Console) in the `Access Token` field.
2. Enter destination (user address) to communicate with.
3. Click on  the button `Set Credentials`
4. Mesibo will now be running and you will be able to send/recieve messages,make audio call/video call.

Thats it! You have sucessfully integrated Flutter with Mesibo.





