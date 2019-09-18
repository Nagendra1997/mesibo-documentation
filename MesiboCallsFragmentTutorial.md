# Mesibo Calls Fragment
Mesibo provides inbuilt UI for your audio and video calls. You just need to add dependencies and you will be ready to make and receive calls using Mesibo. 

## Using Mesibo Calls Fragment


### Install Mesibo Calls API SDK
Include following into dependencies section of your app build.Gradle file, for example:

```java
implementation 'com.mesibo.api:calls:1.0.72'

```

### Initialize Mesibo calls
Initialize Mesibo calls by adding below code in your activity. This will bring whole Mesibo calls infrastructure and you will be able to make calls. 

```java

  MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        
```

### Runtime Permissions

When you receive or make any calls with Mesibo it asks you to grant few permissions those are required to place call. These are- 
You need to give access to this permission as the app may not work without these.

#### - Record Audio - 
This permission is required to access the microphone and record your voice.

#### - Record Video -
This permission is required to make video calls as it access camera while making video call.


### How to make calls

You will be wondering receiving calls is done now how do I make a call to other users? Well, making calls in Mesibo is also very simple just by adding a line.

#### To make Audio call-

Make an audio call by adding below code inside click listener of your call button, suppose you have an AudioCall Button, put this code in its click listener. We are invoking call function on Mesibo calls API. Here, you will see parameters to pass to make Audio call.

call(Context context, long MessageId, UserProfile callId, boolean IsVideoCall)

- First parameter you need to pass is Context,
- Second parameter you need to pass a unique random call id for making calls. This can be generated from `Mesibo.random()`.
- Third parameter is a Mesibo.UserProfile, you can put the address of the user where you want to make call 
  in `MesiboUserProfile.address` and then pass this userProfile in the third parameter of the call function.
- The fourth parameter is a boolean to know if you are making audio or video call, pass `false' for Audio Calls.
	
	 

```java
	Mesibo.UserProfile userProfile = new Mesibo.UserProfile();
        userProfile.address = "User Address";
	
	// Making audio call
	MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(),userProfile, false);
	

```


#### To make Video call -

Similar to audio calls you can make video calls as well, just pass the fourth parameter of call function as `true`

```java
	Mesibo.UserProfile userProfile = new Mesibo.UserProfile();
        userProfile.address = "User Address";
	
	// Making video call
	MesiboCall.getInstance().call(MainActivity.this, Mesibo.random(),userProfile, true);
	

```

That's it! Now with this, you will be able to use Mesibo Calls. Now, when you make or receive call you will see mesibo call fragment visible that performs functions like accept call, decline call and many more.




# Customization of Mesibo calls fragment

Mesibo calls return fragment while incoming and outgoing calls, these fragments display you call UI and handles the action of the user. Since it returns fragment it becomes extremely easy for someone to create own fragment and return custom fragment instead of default fragment. 

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

In your activity, you need to add MesiboCall listener so that your activity can handle incoming or outgoing calls.

```java
        MesiboCall mesiboCall = MesiboCall.getInstance();
        mesiboCall.init(this);
        mesiboCall.setListener(this);
        
```

##Types of Mesibo call Fragments

There are two primitive types of call fragment-

- `MesiboIncomingAudioCallFragment` - This fragment returns a fragment when you receive an audio call in Mesiobo. You will see two buttons - Accept and Decline, User name and picture who is calling you.

- `MesiboVideoCallFragment` - This fragment is visible when you make or receive a video call. You will see user picture, name and buttons to accept and decline.


### Incoming Audio call

As you have implelnted the methods of `MesiboCall.MesiboCallListener` you will find method `MesiboCall_getIncomingAudioCallFragment()`.

This method returns a null Fragment by default that means Mesibo default Audio Incoming fragment will be loaded. Now, to this, if you return your class that overrides `MesiboAudioCallFragment` you will be able to show your custom fragment when you receive an Audio call.

Let's see how this is done - 

#### Create a fragment_audio_cutom.xml 

This XML file will be responsible for the UI of your custom fragment.

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

To your activity where you have implemented `MesiboCall.MesiboCallListener` add below code to return your custom fragment.

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

Great, that's it. Run your app and make receive an audio call, you will see your custom fragment instead of your default fragment.



 
### Incoming/Outgoing Video call

Similar to Audio call Fragment, Video call fragment can also be customized.
As you have implelnted the methods of `MesiboCall.MesiboCallListener` you will find method `MesiboCall_getVideoCallFragment()`.

This method returns a null Fragment by default that means Mesibo default Video Incoming? Outgoing fragment will be loaded. Now, to this, if you return your class that overrides `MesiboVideoCallFragment` you will be able to show your custom fragment for video calls.

Let's see how this is done - 

#### Create a fragment_videocall_custom.xml 

This XML file will be responsible for the UI of your custom fragment.

```java

<?xml version="1.0" encoding="utf-8"?>

<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/rootView"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/call_provider"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="@dimen/_25sdp"
        android:gravity="center"
        android:text="Mesibo Video Call"
        android:textColor="@color/white"
        android:textSize="@dimen/_13sdp" />

    <LinearLayout
        android:id="@+id/photo_layout"
        android:layout_width="@dimen/_60sdp"
        android:layout_height="@dimen/_60sdp"
        android:layout_below="@+id/call_provider"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="@dimen/_10sdp"
        android:gravity="center">

        <ImageView
            android:id="@+id/photo_image"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_weight="1.4"
            android:src="@drawable/profile" />
    </LinearLayout>

    <TextView
        android:id="@+id/call_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/photo_layout"
        android:gravity="center"
        android:text="Shahrukh Sharma"
        android:textColor="@color/white"
        android:textSize="@dimen/_20sdp" />

    <TextView
        android:id="@+id/call_address"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/call_name"
        android:layout_marginTop="@dimen/_5sdp"
        android:gravity="center"
        android:text="+919919191991 | India"
        android:textColor="@color/white"
        android:textSize="@dimen/_15sdp"
        android:visibility="gone" />

    <Chronometer
        android:id="@+id/call_status"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/call_address"
        android:gravity="center"
        android:text="Incoming Video Call"
        android:textColor="@color/white" />


    <LinearLayout
        android:id="@+id/outgoing_call_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_alignWithParentIfMissing="true"
        android:layout_above="@+id/capture_format_slider_call"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="@dimen/_20sdp"
        android:orientation="horizontal"
        android:visibility="gone">

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:layout_marginTop="@dimen/_50sdp"
            android:layout_weight="0.1"
            android:gravity="center"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_margin="@dimen/outgoing_video_call_icon_margin"
                android:layout_weight="1"
                android:background="@drawable/drawable_background_circle"
                android:gravity="center"
                android:padding="@dimen/outgoing_video_call_icon_padding">

                <ImageButton
                    android:id="@+id/button_call_toggle_speaker"
                    android:layout_width="@dimen/outgoing_video_call_icon_size"
                    android:layout_height="@dimen/outgoing_video_call_icon_size"
                    android:layout_gravity="left"
                    android:background="@drawable/ic_volume_up_white_48dp"
                    android:contentDescription="@string/toggle_speaker" />

            </LinearLayout>

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_margin="@dimen/outgoing_video_call_icon_margin"
                android:layout_weight="1"
                android:background="@drawable/drawable_background_circle"
                android:gravity="center"
                android:padding="@dimen/outgoing_video_call_icon_padding">

                <ImageButton
                    android:id="@+id/button_call_switch_camera"
                    android:layout_width="@dimen/outgoing_video_call_icon_size"
                    android:layout_height="@dimen/outgoing_video_call_icon_size"
                    android:layout_gravity="left"
                    android:background="@drawable/ic_switch_camera_white_48dp"
                    android:contentDescription="@string/switch_camera" />

            </LinearLayout>

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_margin="@dimen/outgoing_video_call_icon_margin"
                android:layout_weight="1"
                android:background="@drawable/drawable_background_circle"
                android:gravity="center"
                android:padding="@dimen/outgoing_video_call_icon_padding">

                <ImageButton
                    android:id="@+id/button_call_toggle_camera"
                    android:layout_width="@dimen/outgoing_video_call_icon_size"
                    android:layout_height="@dimen/outgoing_video_call_icon_size"
                    android:layout_gravity="center_horizontal"
                    android:background="@drawable/ic_videocam_off_white_48dp"
                    android:contentDescription="@string/toggle_camera" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_margin="@dimen/outgoing_video_call_icon_margin"
                android:layout_weight="1"
                android:background="@drawable/drawable_background_circle"
                android:gravity="center"
                android:padding="@dimen/outgoing_video_call_icon_padding">

                <ImageButton
                    android:id="@+id/button_call_toggle_mic"
                    android:layout_width="@dimen/outgoing_video_call_icon_size"
                    android:layout_height="@dimen/outgoing_video_call_icon_size"
                    android:layout_gravity="right"
                    android:background="@drawable/ic_mic_off_white_48dp"
                    android:contentDescription="@string/toggle_mic" />
            </LinearLayout>

            <LinearLayout
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:visibility="gone">

                <ImageButton
                    android:id="@+id/button_call_scaling_mode"
                    android:layout_width="@dimen/outgoing_video_call_icon_size"
                    android:layout_height="@dimen/outgoing_video_call_icon_size"
                    android:background="@drawable/ic_fullscreen_exit_white_48dp"
                    android:contentDescription="@string/disconnect_call"
                    android:visibility="invisible" />
            </LinearLayout>
        </LinearLayout>

        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|center_horizontal"
            android:layout_weight="1"
            android:gravity="center">

            <ImageButton
                android:id="@+id/button_call_disconnect"
                android:layout_width="@dimen/_48sdp"
                android:layout_height="@dimen/_48sdp"
                android:background="@drawable/disconnect"
                android:contentDescription="@string/disconnect_call" />
        </LinearLayout>


    </LinearLayout>

    <LinearLayout
        android:id="@+id/incoming_call_container"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignWithParentIfMissing="true"
        android:layout_above="@+id/capture_format_slider_call"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="@dimen/_15sdp"
        android:orientation="horizontal"
        android:visibility="visible">

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:orientation="vertical">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_weight="1.1"
                android:orientation="horizontal"
                android:visibility="gone">

                <LinearLayout
                    android:id="@+id/remind_me_layout"
                    android:layout_width="@dimen/_70sdp"
                    android:layout_height="wrap_content"
                    android:gravity="center"
                    android:orientation="vertical"
                    android:visibility="invisible">

                    <ImageButton
                        android:id="@+id/remind_me"
                        android:layout_width="@dimen/outgoing_video_call_remind_icon_size"
                        android:layout_height="@dimen/outgoing_video_call_remind_icon_size"
                        android:layout_gravity="center"
                        android:background="@android:color/transparent"
                        android:scaleType="centerCrop"
                        android:src="@drawable/ic_alarm_white_36dp" />

                    <TextView
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="center"
                        android:gravity="center"
                        android:text="Remind"
                        android:textColor="@color/white"
                        android:textSize="@dimen/outgoing_video_call_icon_text_size" />
                </LinearLayout>



                <LinearLayout
                    android:layout_width="@dimen/_70sdp"
                    android:layout_height="wrap_content"
                    android:gravity="center"
                    android:orientation="vertical"
                    android:visibility="invisible">

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="match_parent"
                        android:gravity="center"
                        android:orientation="vertical">

                        <ImageButton
                            android:id="@+id/message"
                            android:layout_width="@dimen/outgoing_video_call_remind_icon_size"
                            android:layout_height="@dimen/outgoing_video_call_remind_icon_size"
                            android:background="@android:color/transparent"
                            android:scaleType="centerCrop"
                            android:src="@drawable/ic_message_white_36dp" />

                        <TextView
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:text="Message"
                            android:textColor="@color/white"
                            android:textSize="@dimen/outgoing_video_call_icon_text_size" />
                    </LinearLayout>
                </LinearLayout>

            </LinearLayout>



            <LinearLayout
                android:id="@+id/phone_unlocked"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_marginTop="@dimen/_10sdp"
                android:layout_weight="0.8"
                android:gravity="bottom"
                android:orientation="horizontal"
                android:visibility="visible">

                <LinearLayout
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:gravity="bottom|center"
                    android:orientation="vertical">

                    <ImageView
                        android:id="@+id/declineButton"
                        android:layout_width="@dimen/_40sdp"
                        android:layout_height="@dimen/_40sdp"
                        android:layout_centerInParent="true"
                        android:background="@drawable/round_background_mesibo_color"
                        android:tint="@color/red"
                        app:srcCompat="@drawable/ic_call_end_white_36dp" />


                </LinearLayout>


                <LinearLayout
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:gravity="center"
                    android:orientation="vertical">

                    <ImageView
                        android:id="@+id/accept_button"
                        android:layout_width="@dimen/_60sdp"
                        android:layout_height="@dimen/_60sdp"
                        android:layout_centerInParent="true"
                        android:background="@drawable/round_background_mesibo_color"
                        android:tint="@color/white"
                        app:srcCompat="@drawable/ic_videocam_white_24dp"/>

                </LinearLayout>

                <LinearLayout
                    android:id="@+id/incoming_audio_accept_container"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:gravity="center"
                    android:orientation="vertical"
                    android:visibility="visible">

                    <LinearLayout
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:gravity="bottom|center"
                        android:orientation="vertical">

                        <ImageView
                            android:id="@+id/custom_message_button"
                            android:layout_width="@dimen/_40sdp"
                            android:layout_height="@dimen/_40sdp"
                            android:background="@drawable/round_background_mesibo_color"
                            android:tint="@color/white"
                            app:srcCompat="@drawable/ic_message_white_36dp" />

                    </LinearLayout>
                </LinearLayout>


            </LinearLayout>


        </LinearLayout>
    </LinearLayout>

    <SeekBar
        android:id="@+id/capture_format_slider_call"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        android:layout_margin="8dp"
        android:progress="50"
        android:visibility="gone" />
</RelativeLayout>





```


#### Create a VideoCallFragment.java 

Create a VideoCallFragment.java that extends `MesiboVideoCallFragment`

```java 
public class VideoCallFragment extends MesiboVideoCallFragment implements Mesibo.CallListener, View.OnTouchListener {

    private ImageButton cameraSwitchButton;
    private ImageButton videoScalingButton;
    private ImageButton toggleCameraButton;
    private ImageButton toggleMuteButton;
    private ImageButton toggleSpeakerButton;
    ImageView imageView, mDeclineViewButton, mAcceptViewButton, mDefaultMessageButton;
    private OnCallEvents callEvents;
    private ScalingType scalingType;
    Mesibo.UserProfile mProfile;
    private View mIncomingView, mInProgressView;


    @Override
    public View onCreateView(
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View controlView = inflater.inflate(R.layout.fragment_videocall_custom, container, false);

        TextView contactView = controlView.findViewById(R.id.call_name);
        imageView = controlView.findViewById(R.id.photo_image);
        ImageButton disconnectButton = controlView.findViewById(R.id.button_call_disconnect);
        cameraSwitchButton = controlView.findViewById(R.id.button_call_switch_camera);
        videoScalingButton = controlView.findViewById(R.id.button_call_scaling_mode);
        toggleSpeakerButton = controlView.findViewById(R.id.button_call_toggle_speaker);
        toggleCameraButton = controlView.findViewById(R.id.button_call_toggle_camera);
        toggleMuteButton = controlView.findViewById(R.id.button_call_toggle_mic);
        mIncomingView = controlView.findViewById(R.id.incoming_call_container);
        mInProgressView = controlView.findViewById(R.id.outgoing_call_container);
        mDeclineViewButton = controlView.findViewById(R.id.declineButton);
        mAcceptViewButton = controlView.findViewById(R.id.accept_button);
        mDefaultMessageButton = controlView.findViewById(R.id.custom_message_button);

        // Add buttons click events.
        disconnectButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                hangup();


            }
        });

        cameraSwitchButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                switchCamera();
            }
        });

        videoScalingButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (scalingType == ScalingType.SCALE_ASPECT_FILL) {
                    videoScalingButton.setBackgroundResource(R.drawable.ic_fullscreen_white_48dp);
                    scalingType = ScalingType.SCALE_ASPECT_FIT;
                } else {
                    videoScalingButton.setBackgroundResource(R.drawable.ic_fullscreen_exit_white_48dp);
                    scalingType = ScalingType.SCALE_ASPECT_FILL;
                }
                ///callEvents.onVideoScalingSwitch(scalingType);
                scaleVideo(true);
            }
        });
        scalingType = ScalingType.SCALE_ASPECT_FILL;

        toggleSpeakerButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                toggleSpeaker();
                boolean enabled = callEvents.onToggleSpeaker();
                toggleSpeakerButton.setAlpha(enabled ? 1.0f : 0.3f);
                callEvents.onToggleSpeaker();


            }
        });

        toggleMuteButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                toggleMic();
                boolean enabled = callEvents.onToggleMic();
                toggleMuteButton.setAlpha(enabled ? 1.0f : 0.3f);
                callEvents.onToggleMic();

            }
        });

        toggleCameraButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                toggleCamera();
                boolean enabled = callEvents.onToggleCamera();
                //setButton(toggleCameraButton, enabled);
                toggleCameraButton.setAlpha(enabled ? 1.0f : 0.3f);
                callEvents.onToggleCamera();


            }
        });

        mDeclineViewButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                hangup();
            }
        });

        mAcceptViewButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                answer(true);
                setDisplayMode();
            }
        });

        mDefaultMessageButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
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
                                hangup();
                                break;
                        }
                    }
                });

                AlertDialog dialog_cust = builder.create();
                dialog_cust.show();
            }
        });


//        contactView.setText(mProfile.name);
        contactView.setText("Mr. John");
        setUserPicture();
        Chronometer statusView = (Chronometer) controlView.findViewById(R.id.call_status);
        setStatusView(statusView);
        setDisplayMode();

        return controlView;
    }


    private void setDisplayMode() {

        boolean incoming = (isIncoming() && !isAnswered());
        mIncomingView.setVisibility(incoming ? View.VISIBLE : View.GONE);
        mInProgressView.setVisibility(incoming ? View.GONE : View.VISIBLE);

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
                imageView.setImageDrawable(MesiboUtils.getRoundImageDrawable(b));
            }
        } else {
            //TBD, getActivity.getresource crashes sometime if activity is closing
            imageView.setImageDrawable(MesiboUtils.getRoundImageDrawable(BitmapFactory.decodeResource(getActivity().getResources(), R.drawable.profile)));
        }
    }


    private void sendCustomeMessage(String peer, String message) {

        Mesibo.MessageParams messageParams = new Mesibo.MessageParams();
        messageParams.setPeer(peer);
        Mesibo.sendMessage(messageParams, Mesibo.random(), message);
        hangup();

    }


    @Override
    public void onStart() {
        super.onStart();

        boolean captureSliderEnabled = false;
        Bundle args = getArguments();
        if (args != null) {
        }
        boolean videoCallEnabled = true;
        if (!videoCallEnabled) {
            cameraSwitchButton.setVisibility(View.INVISIBLE);
        }
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
    }

    // TODO(sakal): Replace with onAttach(Context) once we only support API level 23+.
    @SuppressWarnings("deprecation")
    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);
        callEvents = (OnCallEvents) activity;
    }

    @Override
    public boolean Mesibo_onCall(long peerid, long callid, Mesibo.UserProfile userProfile, int i) {
        return false;
    }

    @Override
    public boolean Mesibo_onCallStatus(long peerid, long callid, int status, int flags, String desc) {

        return false;
    }

    @Override
    public void Mesibo_onCallServer(int type, String url, String username, String credential) {

    }

    @Override
    public boolean onTouch(View view, MotionEvent motionEvent) {
        return false;
    }
}

```

#### Return `VideoCallFragment` in `MesiboCall_getVideoCallFragment()`

To your activity where you have implemented `MesiboCall.MesiboCallListener` add below code to return your custom fragment.

```java

    @Override
    public MesiboVideoCallFragment MesiboCall_getVideoCallFragment(Mesibo.UserProfile userProfile) {

        VideoCallFragment videoCallFragment = new VideoCallFragment();
        videoCallFragment.setProfile(userProfile);

        return videoCallFragment;
	//return null;
    }

```

This example looks like this -


<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/video_call.png" width="320" height="550">



That's it. Run your app and make Video calls. You will see your custom created fragment for video calls.

# Conclusion

You’ve seen how to use Mesibo call Fragments and created fully custom call fragments for video and audio calls.
