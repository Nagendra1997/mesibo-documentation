# Mesibo Calls Fragment
Mesibo provides inbuilt UI for your audio and video calls. Mesibo calls are loaded using fragment, these fragment can also be loaded in your activity.

## Using Mesibo Calls Fragment

There are two types of call fragment-

- `MesiboIncomingAudioCallFragment` - This fragment returns a fragemnt when you receive a audio call in Mesiobo. You will see two buttons - Accept and Decline, User name and picture who is calling you.

- `MesiboVideoCallFragment` - This fragment is visible when you make or receive a video call. You will see user picture, name and buttons to accept and decline.


To use Mesibo call fragments - 

### Install Mesibo Calls API SDK
Include following into dependencies section of your app build.gradle file, for example:

```java
implementation 'com.mesibo.api:calls:1.0.72'

```

### Intialize Mesibo calls

```java

  MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        
```

Now with this you will be able to use Mesibo Calls, At first call you need to access phone permission - Record audio, Telephone for Audio calls and Record Video for video calls. You need to accept these permission else your app will not be able to make or recieve calls.


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



