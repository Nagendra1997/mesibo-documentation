# Mesibo Calls Fragment
Mesibo provides inbuilt UI for your audio and video calls. You just need to add dependencies and you will be ready to make and recive calls using Mesibo. 

## Using Mesibo Calls Fragment


### Install Mesibo Calls API SDK
Include following into dependencies section of your app build.gradle file, for example:

```java
implementation 'com.mesibo.api:calls:1.0.72'

```

### Intialize Mesibo calls
Initialize Mesibo calls by adding below code in your activity. This will bring whole Mesibo calls infrastructure and you will be able to make calls. 

```java

  MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        
```

### Runtime Permissions

When you receive or make any calls with Mesibo it asks you to grant few permissions those are required to place call. These are- 
You need to give access to these permission as app may not work without these.

#### - Record Audio - 
This permission is required to access microphone and reord your voice.

#### - Record Audio -
This permission is required to make video call as it access camera while makeing video call.



That's it! Now with this you will be able to use Mesibo Calls. When you recieve calls you will see Mesibo calls fragment.

### How to make calls

You will be wondering receiving calls is done now how do i make a call to other users? Well, making call in Mesibo is also very simple just by adding a line.

#### To make Audio call-

Make a audio call by adding below code inside click listerner of your call button, suppose you have a AudioCall Button, put this code in its click listener. Basically we are invoking call funtion on Mesibo calls api. Here, you will see parameters to pass to make Audio call.

call(Context context, long MessageId, UserProfile callId, boolean IsVideoCall)

- First parameter you need to pass is Context,
- Second parameter you need to pass a unique random call id for making calls. This can be generated from `Mesibo.random()`.
- Third parameter is a Mesibo.UserProfile, you can put address of the user where you want to make call 
  in `MesiboUserProfile.address` and then pass this userProfile in third parameter of the call function.
- Fourth parameter is a boolean to know if you are making audio or video call, pass `false' for Audio Calls.
	
	 

```java
	Mesibo.UserProfile userProfile = new Mesibo.UserProfile();
        userProfile.address = "User Address";
	
	// Making audio call
	MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(),userProfile, false);
	

```


#### To make Video call

Similar to audio calls you can make video calls as well , just pass fourth paarameter of call funtion as `true`

```java
	Mesibo.UserProfile userProfile = new Mesibo.UserProfile();
        userProfile.address = "User Address";
	
	// Making video call
	MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(),userProfile, true);
	

```





There are two types of call fragment-

- `MesiboIncomingAudioCallFragment` - This fragment returns a fragemnt when you receive a audio call in Mesiobo. You will see two buttons - Accept and Decline, User name and picture who is calling you.

- `MesiboVideoCallFragment` - This fragment is visible when you make or receive a video call. You will see user picture, name and buttons to accept and decline.


To use Mesibo call fragments - 





### Extend your activity to implement `MesiboCall.MesiboCallListener`
In your activity implement  `MesiboCall.MesiboCallListener` and add the methods.

```java 
public class ExampleActivity extends AppCompatActivity implements MesiboCall.MesiboCallListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
	      setContentView(R.layout.activity_main);
	        
    }
    
    @Override
    public MesiboVideoCallFragment MesiboCall_getVideoCallFragment(Mesibo.UserProfile userProfile) {
       return null;
    }

    @Override
    public MesiboAudioCallFragment MesiboCall_getAudioCallFragment(Mesibo.UserProfile userProfile) {
        return null;
    }

    @Override
    public Fragment MesiboCall_getIncomingAudioCallFragment(Mesibo.UserProfile userProfile) {
      return null;
    }
}

```

To your activity you need to add MesiboCall listener so that your activity is able to handle incoming or outgoing calls.

```java
        MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        mesiboCall.setListener(this);
        
```



# Customization of Mesibo calls fragment

Mesibo calls returns fragment while incoming and outgoing calls, these fragments display you calls UI and handles action of the user. Since it returns fragment it becomes extreamly easy for someone to create own fragment and return custom fragment instead of deafualt fragment. 

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/audio_call.png" width="320" height="550">


<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/video_call.png" width="320" height="550">

