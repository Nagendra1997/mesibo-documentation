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

#### - Record Video -
This permission is required to make video call as it access camera while makeing video call.


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


#### To make Video call -

Similar to audio calls you can make video calls as well , just pass fourth paarameter of call funtion as `true`

```java
	Mesibo.UserProfile userProfile = new Mesibo.UserProfile();
        userProfile.address = "User Address";
	
	// Making video call
	MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(),userProfile, true);
	

```

That's it! Now with this you will be able to use Mesibo Calls. Now, when you make or receive call you will see mesibo call fragment visible that performs functions like accept call, decline call and many more.




# Customization of Mesibo calls fragment

Mesibo calls returns fragment while incoming and outgoing calls, these fragments display you calls UI and handles action of the user. Since it returns fragment it becomes extreamly easy for someone to create own fragment and return custom fragment instead of deafualt fragment. 

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

In your activity you need to add MesiboCall listener so that your activity is able to handle incoming or outgoing calls.

```java
        MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        mesiboCall.setListener(this);
        
```

##Types of Mesibo call Fragments

There are two premitive types of call fragment-

- `MesiboIncomingAudioCallFragment` - This fragment returns a fragemnt when you receive a audio call in Mesiobo. You will see two buttons - Accept and Decline, User name and picture who is calling you.

- `MesiboVideoCallFragment` - This fragment is visible when you make or receive a video call. You will see user picture, name and buttons to accept and decline.


### Incoming Audio call

As you have implelnted the methods of `MesiboCall.MesiboCallListener` you will find method `MesiboCall_getIncomingAudioCallFragment()`.

This method return a null Fragment by deafault that means Mesibo default Audio Incoming fragment will be loaded. Now, to this if you return you own class that overides `MesiboAudioCallFragment` you will be able to show your own custom fragment when you receive an Audio call.

Let's see how this is done - 

#### Create a fragment_audio_cutom.xml 

This xml file will be reponsible for the UI of your custom fragment.

```java

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/incoming_fragment_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="@color/white">

    <LinearLayout
        android:id="@+id/incoming_fragment_background"
        android:layout_width="match_parent"
        android:layout_height="@dimen/_300sdp"
        android:background="@color/mesibo"
        android:gravity="center"
        android:orientation="vertical">


        <LinearLayout
            android:layout_width="@dimen/_70sdp"
            android:layout_height="@dimen/_70sdp"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="@dimen/_15sdp">

            <ImageView
                android:id="@+id/photo_image"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_weight="1.4"
                android:src="@drawable/profile" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="@dimen/_15sdp"
            android:gravity="center"
            android:orientation="vertical">


            <TextView
                android:id="@+id/incoming_name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:gravity="left"
                android:text="Rakesh Sharma"
                android:textColor="@color/white"
                android:textSize="@dimen/_20sdp" />

            <TextView

                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:gravity="left"
                android:text="Incoming Call"
                android:textColor="@color/white"
                android:textSize="@dimen/_12sdp" />

        </LinearLayout>

        <TextView
            android:id="@+id/tvVideCall"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="@dimen/_2sdp"
            android:gravity="center"
            android:text="Mesibo Audio Call"
            android:textColor="@color/white"
            android:textSize="@dimen/_15sdp" />


    </LinearLayout>

    <LinearLayout
        android:id="@+id/phone_unlocked"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_marginTop="@dimen/_10sdp"
        android:gravity="bottom"
        android:paddingBottom="@dimen/_20sdp"
        android:visibility="visible">


        <RelativeLayout
            android:id="@+id/decline_view_layout"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_weight="1">

            <ImageView
                android:layout_width="@dimen/incomming_video_call_icon"
                android:layout_height="@dimen/incomming_video_call_icon"
                android:layout_centerInParent="true"
                android:src="@drawable/round_background_mesibo_color" />


            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:tint="@color/red"
                app:srcCompat="@drawable/ic_call_end_white_36dp" />


        </RelativeLayout>


        <RelativeLayout
            android:id="@+id/accept_view_layout"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_weight="1">

            <ImageView
                android:layout_width="@dimen/_60sdp"
                android:layout_height="@dimen/_60sdp"
                android:layout_centerInParent="true"
                android:src="@drawable/round_background_mesibo_color" />


            <ImageView
                android:layout_width="@dimen/_35sdp"
                android:layout_height="@dimen/_35sdp"
                android:layout_centerInParent="true"
                android:tint="@color/white"
                app:srcCompat="@drawable/ic_action_audiocall" />


        </RelativeLayout>


        <RelativeLayout
            android:id="@+id/custom_message_view_layout"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_weight="1">

            <ImageView
                android:layout_width="@dimen/incomming_video_call_icon"
                android:layout_height="@dimen/incomming_video_call_icon"
                android:layout_centerInParent="true"
                android:src="@drawable/round_background_mesibo_color" />


            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:scaleX="0.8"
                android:scaleY="0.8"
                android:tint="@color/white"
                app:srcCompat="@drawable/ic_message_white_36dp" />


        </RelativeLayout>


    </LinearLayout>


</RelativeLayout>




```


#### Create a AudioIncomingFragment.java 

Create a AudioIncomingFragment.java that extends `MesiboIncomingAudioCallFragment`

```java 
public class AudioIncomingFragment extends MesiboIncomingAudioCallFragment implements Mesibo.CallListener {

    private ImageView photoImage;
    private Mesibo.UserProfile mProfile;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_audio_custom, container, false);
        initializeView(view);
        return view;
    }

    private void initializeView(View view) {
        TextView mName = view.findViewById(com.mesibo.calls.R.id.incoming_name);
        photoImage = view.findViewById(com.mesibo.calls.R.id.photo_image);

        RelativeLayout declineViewButton = view.findViewById(R.id.decline_view_layout);
        RelativeLayout dcceptViewButton = view.findViewById(R.id.accept_view_layout);
        RelativeLayout defaultMessageButton = view.findViewById(R.id.custom_message_view_layout);


        declineViewButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                callHangup();
            }
        });

        dcceptViewButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                callAnswer();

            }
        });


        defaultMessageButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                sendCustomMessageOnCall();
            }
        });

        mName.setText(mProfile.name);
        setUserPicture();

    }


    public void setProfile(Mesibo.UserProfile profile) {

        mProfile = profile;

    }

    void setUserPicture() {
        String filePath = Mesibo.getUserProfilePicturePath(mProfile, Mesibo.FileInfo.TYPE_AUTO);

        Bitmap b;
        if (Mesibo.fileExists(filePath)) {
            b = BitmapFactory.decodeFile(filePath);
            if (null != b) {
                photoImage.setImageDrawable(MesiboUtils.getRoundImageDrawable(b));
            }
        } else {
            //TBD, getActivity.getresource crashes sometime if activity is closing
            photoImage.setImageDrawable(MesiboUtils.getRoundImageDrawable(BitmapFactory.decodeResource(getActivity().getResources(), R.drawable.profile)));
        }
    }


    private void sendCustomeMessage(String peer, String message) {

        Mesibo.MessageParams messageParams = new Mesibo.MessageParams();
        messageParams.setPeer(peer);
        Mesibo.sendMessage(messageParams, Mesibo.random(), message);
        callHangup();


    }


    public void callHangup() {
        hangup();
        getActivity().finish();
    }


    public void callAnswer() {

        answer();

    }

    public void sendCustomMessageOnCall() {
        // setup the alert builder
        AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());


        String[] DefaultTextMessages = {
                "Can't talk now. What's up?",
                "I'll call you right back.",
                "I'll call you later.",
                "Can't talk now. Call me later.",
                "Custom message..."};
        builder.setItems(DefaultTextMessages, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {

                String peer = mProfile.address;

                switch (which) {
                    case 0: //
                        sendCustomeMessage(peer, "Can't talk now. What's up?");
                        break;
                    case 1: //
                        sendCustomeMessage(peer, "I'll call you right back.");
                        break;
                    case 2: //
                        sendCustomeMessage(peer, "I'll call you later.");
                        break;
                    case 3: //
                        sendCustomeMessage(peer, "Can't talk now. Call me later.");
                        break;
                    case 4: //Custom message
                        Intent i = new Intent(getActivity(), MessagingActivity.class);
                        i.putExtra("peer", peer);
                        startActivity(i);

                        break;
                }
                callHangup();
            }
        });


        AlertDialog dialog = builder.create();
        dialog.show();
    }


    @Override
    public void onResume() {
        super.onResume();
        Mesibo.addListener(this);
    }

    @Override
    public void onPause() {
        super.onPause();
        Mesibo.removeListener(this);
        stopIncomingNotification();

    }


    @Override
    public boolean Mesibo_onCall(long peerid, long callid, Mesibo.UserProfile profile, int flags) {
        return true;
    }

    @Override
    public boolean Mesibo_onCallStatus(long peerid, long callid, int status, int flags, String desc) {
        if ((status & CALLSTATUS_COMPLETE) > 0) {
            getActivity().finish();
        }
        return true;
    }

    @Override
    public void Mesibo_onCallServer(int type, String url, String username, String credential) {

    }
}


```

#### Return `AudioIncomingFragment` in `MesiboCall_getIncomingAudioCallFragment()`

To your activity where you have implemented `MesiboCall.MesiboCallListener` add below code to return your own custom fragment.

```java

    @Override
    public Fragment MesiboCall_getIncomingAudioCallFragment(Mesibo.UserProfile userProfile) {
    
        AudioIncomingFragment audioIncomingFragment = new AudioIncomingFragment();
        audioIncomingFragment.setProfile(userProfile);

        return audioIncomingFragment;
       // return null;
    }
```

This example looks like this -

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/audio_call.png" width="320" height="550">

Great, that's it. Run your app and make receive an audio call, you will see your custom frgament instead you deafault fragment.



 
### Incoming/Outgoing Video call




<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/video_call.png" width="320" height="550">

