
## Using MesiboUI
Mesibo offers you ready to use UI modules which you can use it in your app. Mesibo UI modules are completely customizable. MesiboUI consist fragment that can be loaded in your activity and you will be able to see Mesibo chatUI and UserListUI in no time. MesibiUI is a fragment that can be loaded anywhere. You can load it from anywhere in your app just by loading a fragment in your activity.

## Mesibo UI Fragments

There are primarily two types of UI fragment
 - User and Message List Fragment, which displays all the users and messages 
 - Message Fragment, which renders all the messages for a particular user or a group

You can use these fragments in your code as described below.

### User and Message List Fragment:
`MesiboUserListFragment` displays a list of users and groups. One can click on the user/group to open Mesibo chat view. 	`MesibouserListFragment` is designed in such a way that the user has access to all the functions like creating group, viewing all the user list and much more. Even if one wants to create and modify this, Mesibo provides the option to do so.

### Messaging Fragment
`MesiboMessagingFragment` renders all the messages for a particular user or a group which you can load in your application as it suits you. 


## Loading MesiboUI in your activity:

Mesibo provides 5 'Modes' that can be passed as argument of the fragment and the desired fragment can be loaded. The modes are -


 1. MesiboUI.USERLIST_GROUPSELECTION_MODE;
 2. MesiboUI.USERLIST_EDIT_GROUP_MODE;
 3. MesiboUI.USERLIST_FORWARD_MODE;
 4. MesiboUI.USERLIST_NEWCONTACTS_MODE;
 5. MesiboUI.USERLIST_MESSAGELIST_MODE;
 
 - `USERLIST_GROUPSELECTION_MODE` launches UI for selecting and creating a group. 
 - `USERLIST_EDIT_GROUP_MODE` launches editing group UI.
 - `USERLIST_FORWARD_MODE` populates forward UI list of users to whom you can forward your message or file.
 - `USERLIST_NEWCONTACTS_MODE` launches UI to select new contact from user list to start chat. 
 - `USERLIST_MESSAGELIST_MODE` launches a history of all your chats.
 
 Some extra arguments are also there for other actions-
 
 - `MesiboUI.MESSAGE_ID` - MessageId is the id of the message. You will need this when you load MesiboUI with   					  `USERLIST_FORWARD_MODE`, this loads message forward fragment where a selected message can be forwarded to other contacts in the list. MessageId is the message that has to be forwarded. 
 
 - `MesiboUI.MESSAGE_IDS`- Multiple messages can also be forwarded at a time. `MesiboUI.MESSAGE_IDS` is an array of message Ids that has to forwarded to other users.
 
 - `MesiboUI.MESSAGE_CONTENT`- Pass the message with these arguments. This contains the message to forward.
 
 - `MesiboUI.FORWARD_AND_CLOSE` - Use this argument to forward your message to other users and then close the 							  MesiboForwardFragment after forwarding message is done.


Let's see above things with an example- 

Create an activity say `YourActivity` that implements `MesiboMessageListFragment.FragmentListener`

```java
public class YourActivity extends AppCompatActivity implements MesiboMessageListFragment.FragmentListener {
    public static final String TAG="MesiboMainActivity";
    TextView contactsTitle = null;
    TextView contactsSubTitle = null;
    int mMode = 0;
    long mForwardId = 0;
    long[] mForwardIds;
    Bundle mEditGroupBundle = null;
    boolean mHideHomeBtn = false;
    boolean mKeepRunning = false;
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
	
	mMode = getIntent().getIntExtra(MesiboUI.MESSAGE_LIST_MODE, MesiboUI.USERLIST_MESSAGELIST_MODE);
        mForwardId = getIntent().getLongExtra(MesiboUI.MESSAGE_ID, 0);
        mForwardIds = getIntent().getLongArrayExtra(MesiboUI.MESSAGE_IDS);
        String forwardMessage = getIntent().getStringExtra(MesiboUI.MESSAGE_CONTENT);
        boolean forwardAndClose = getIntent().getBooleanExtra(MesiboUI.FORWARD_AND_CLOSE, false);
      
        if(mMode == MesiboUI.USERLIST_EDIT_GROUP_MODE)
            mEditGroupBundle = getIntent().getBundleExtra(MesiboUI.BUNDLE);

        setContentView(R.layout.activity_messages);
        mMesiboUIOptions = MesiboUI.getConfig();

        if(mMode == MesiboUI.USERLIST_MESSAGELIST_MODE) {
            contactsSubTitle.setText(mMesiboUIOptions.offlineIndicationTitle);
            getSupportActionBar().setDisplayHomeAsUpEnabled(mMesiboUIOptions.enableBackButton);
        }

        if(savedInstanceState == null) {

            UserListFragment userListFragment = new UserListFragment();
            userListFragment.setListener(this);
            Bundle bl = new Bundle();
            bl.putInt(MesiboUI.MESSAGE_LIST_MODE, mMode);
            bl.putLong(MesiboUI.MESSAGE_ID, mForwardId);

            if(!TextUtils.isEmpty(forwardMessage))
                bl.putString(MesiboUI.MESSAGE_CONTENT, forwardMessage);

            bl.putLongArray(MesiboUI.MESSAGE_IDS, mForwardIds);
            if(mMode == MesiboUI.USERLIST_EDIT_GROUP_MODE)
                bl.putBundle(MesiboUI.BUNDLE, mEditGroupBundle);

            bl.putBoolean(MesiboUI.FORWARD_AND_CLOSE, forwardAndClose);

            userListFragment.setArguments(bl);
            FragmentManager fm = getSupportFragmentManager();
            FragmentTransaction ft = fm.beginTransaction();
            ft.replace(R.id.userlist_fragment, userListFragment,"null");
            ft.addToBackStack("userListFragment");
            ft.commit();

        }
	
	}
	
 @Override
    public boolean Mesibo_onClickUser(String address, long groupid, long forwardid) {
        return false;
    }

 @Override
    public boolean Mesibo_onUserListFilter(Mesibo.MessageParams params) {
        return false;
    }
}

```	

Now, starting `YourActicivity` will load MesiboUI fragments as per the `Modes` you have passed as arguments to this fragment. As mentioned earlier MesiboUI can be loaded from anywhere in your app, you just need to start `YourActivity` and load the fragment.
You can start activity as -

```java 
  public static void launchYourActivity(Context context, long forwardid, int selectionMode, int flag, boolean startInBackground, boolean keepRunning, Bundle bundle, String forwardMessage) {
        Intent intent = new Intent(context, YourActivity.class);
        intent.putExtra(MesiboUI.MESSAGE_LIST_MODE, selectionMode)
            .putExtra(MesiboUI.MESSAGE_ID, forwardid)
        
        if(!TextUtils.isEmpty(forwardMessage)) {
            intent.putExtra(MesiboUI.MESSAGE_CONTENT, forwardMessage);
        }

        if(flag > 0)
            intent.setFlags(flag);

        if(null != bundle)
            intent.putExtra(MesiboUI.BUNDLE,bundle);
        context.startActivity(intent);
    }
    
  ```


Mesibo has all modes required by the user to minimize the effort of customizing and development. Even after this if you like to customize your and have your UI you are welcome to do so. Customization we will see further in this section.


Now, you have a general idea of how we load Mesibo fragments from your own created activity. Let's see how to load  `MessageFragment`.

## Using Mesibo MessagingFragment

MessagingFragment loads chat view when you click on any of the existing chat or start a new chat. Mesibo MessagingFragment renders all messages and populate a recycler view with messages. 

As you have seen above how simple is it to load MessageUserList, similarly loading MessagingFragment is also extremely easy, `YourActivity` implements `MesiboMessageListFragment.FragmentListener` that gives you 2 methods - 

- `Mesibo_onClickUser()` - This method handles users click on the user list. When you click on any of the User from user list, this method is invoked and it returns false which means it returns default chat view to you. 
Now, Using MesiboMessaging Frgamnet, You need to start mesibo `MessagingActivity` and pass your argument. You need to return true in this case to load Mesibo Messaging fragment.


Inside `Mesibo_onClickUser()` method of your Activity

```java
 @Override
    public boolean Mesibo_onClickUser(String address, long groupid, long forwardid) {
    
     Intent intent = new Intent(context, MessagingActivity.class);
        intent.putExtra(MesiboUI.PEER, userAddress)
            .putExtra(MesiboUI.GROUP_ID, groupId)
           startActivity(intent);
    
        return true;
    }
    
  ```
  
  Here MesagingActivity is the default activity of Mesibo to Load chat view, you need to pass UserAddress(Destination) as value of `MesiboUI.PEER` and if its group you need to pass group id as value of `MesiboUI.GROUP_ID`.
  
  MessagingActivity gets the peer and group value from intent and passes it as an argument to load MesiboMessaging fragment. When you start MessagingActivity you will see complete chat view for the user you have passed in MesiboUI.PEER. You can make audio and video calls, send files, send location, emojis and many more exciting things.
  
  Now, you have seen how easy it is to load MesiboUI from anywhere in your app. Mesibo has all required fragments by default but if you need to modify you can see our 'Customization of MesiboUI' tutorial next. 
  
  

# Customization of MesiboUi

Mesibo allows you to customize your chat views as per your app theme and requirement. Since MesiboMessaging is a fragment it can be loaded anywhere and it is even easy to modify.  


The two basic elements that form a chat application are

- Incoming chat view
	Incoming chat view is a view that you see when you receive a message. By default, this view has everything from displaying received messages to displaying files and file types. Again, if you want to customize as per your theme and requirement you are open to do so.
	
- Outgoing chat view
	Outgoing chat view is a view that you see when you send a message. Similar to incoming chat view this view also has different options to display outgoing content. For example, based on your message status this view can be used to display status "ticks" - single tick, double tick, blue tick. You can customize an outgoing view as per your requirement.
	
	
Mesibo chat view is a recycler view that contains the incoming chat view and outgoing chat view, which adds Items(here messages) when a message is sent or received. All the messages are rendered one after another to load in recycler view with items one by one. As you scroll recycler view, more items are added based on the history of your chat. Using Mesibo recycler view here for displaying message gives advantage of not rendering and loading all the messages at a time, it gets loaded based on your scroll position. Customizing these views can be achieved easily let us see how it is done.


###Follow the steps below to customize your Mesibo Application UI.

## Creating UI elements

To have your custom chat layout, you need to create your own MessagingActivity class and UIFragment class. In this tutorial, we will be calling them `MessagingActivity` and `MessagingUiFragment`.

`MessagingActivity.java` loads `MessagingUIFragment` which has MesiboRecyclerView and Adapter and you start `MessagingActvit

Let us implement them one by one:

## 1. Create MessagingUIFragment.java

`MesiboUiFragment` extends `MesiboMessagingFragment` present in the core Mesibo Library.
Here, we will be creating our custom view. The UI will be updated from this fragment. 

The basic function structure should look like below. This is similar to the implementation of a RecyclerView and Adapter.

```java
public class MessagingUiFragment extends MesiboMessagingFragment implements MesiboRecycleViewHolder.Listener {
   @Override
   public int Mesibo_onGetItemViewType(Mesibo.MessageParams messageParams, String s) {

       return 0;
   }

   @Override
   public MesiboRecycleViewHolder Mesibo_onCreateViewHolder(ViewGroup viewGroup, int i) {
       return null;
   }

   @Override
   public void Mesibo_onBindViewHolder(MesiboRecycleViewHolder mesiboRecycleViewHolder, int i, boolean b, Mesibo.MessageParams messageParams, Mesibo.MesiboMessage mesiboMessage) {

   }

   @Override
   public void Mesibo_UpdateViewHolder(MesiboRecycleViewHolder mesiboRecycleViewHolder, Mesibo.MesiboMessage mesiboMessage) {

   }

   @Override
   public void Mesibo_onViewRecycled(MesiboRecycleViewHolder mesiboRecycleViewHolder) {

   }
}
```
Now, we will look into each of these methods.

### Mesibo_onGetItemViewType

This method returns the type of message i.e Incoming message or Outgoing message. The returned type is then checked in `Mesibo_onCreateViewHolder()`  and then the custom view is added to recycler view accordingly.

```java
 if (messageParams.isIncoming()) {

       return MesiboRecycleViewHolder.TYPE_INCOMING;

   }
   return MesiboRecycleViewHolder.TYPE_OUTGOING;
```


### Mesibo_onCreateViewHolder

In this method type of message(Incoming and Outgoing) is checked. Based on the type you can return your custom view or return null to load default view.
```java
  if (MesiboRecycleViewHolder.TYPE_INCOMING == type) {
           View v = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.incoming_chat_layout, viewGroup, false);

           return new IncomingMessgaeViewHolder(v);

       }
       return null;
```

### Mesibo_onBindViewHolder()

This method binds the returned view and messages.

```java
if (type == MesiboRecycleViewHolder.TYPE_INCOMING) {

   IncomingMessgaeViewHolder IncomingView = (IncomingMessgaeViewHolder) mesiboRecycleViewHolder;
IncomingView.mIncomingMessageTV.setText(Html.fromHtml(mesiboMessage.message));

}
```

## Create a custom row Layout inside MessagingUIFragment.java 

The UI customization can be done by creating a view holder with different types like incoming view, outgoing view, etc which will be returned in `Mesibo_onCreateViewHolder`.
To customize your view holder, you need to create a row Layout.

First, create a file named `incoming_chat_layout.xml` that describes the display component attributes. A sample XML file with TextView which is used to show the message(incoming) is as follows:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
   xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content">
   <FrameLayout
       android:id="@+id/incoming_layout_bubble"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_marginLeft="8dp"
       android:layout_marginRight="60dp"
       android:layout_alignParentLeft="true"
       android:layout_marginTop="@dimen/_1sdp"
       android:background="@drawable/balloon_incoming_normal">


      
       <TextView
           android:id="@+id/textview_message"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:paddingLeft="@dimen/_15sdp"
           android:paddingRight="@dimen/_15sdp"
           android:paddingTop="@dimen/_1sdp"
           android:paddingBottom="@dimen/_5sdp"
           android:textSize="@dimen/_15sdp"
           android:textColor="@color/black_overlay"
           android:autoLink="web"
           android:text="Text Message goes here"
           android:visibility="visible"/>
       </LinearLayout>
   </FrameLayout>

</RelativeLayout>


}
```
Depending on what you require custom views can be created for header view, outgoing view, custom view or any other view.
Now, you need to add your custom view holder to your fragment.

### Creating the ViewHolder
The MesiboRecyclerView uses a ViewHolder to store the references to the relevant views for one entry in the RecyclerView. This solution avoids all the findViewById() method calls.



```java
public class IncomingMessgaeViewHolder extends MesiboRecycleViewHolder {
   TextView mIncomingMessageTV
   View mViewIncomingMessage;

   public IncomingMessgaeViewHolder(View v) {
       super(v);
       mIncomingMessageTV = v.findViewById(R.id.textview_message);
            mViewIncomingMessage = v;
   }
}
```

You can find the complete code for your `MessagingUIFragment` below.

### Full MessagingUIFragment() code
```java
public class MessagingUiFragment extends MesiboMessagingFragment implements MesiboRecycleViewHolder.Listener {

public class IncomingMessgaeViewHolder extends MesiboRecycleViewHolder {

   TextView mIncomingMessageTV
   View mViewIncomingMessage;

   public IncomingMessgaeViewHolder(View v) {
       super(v);
       mIncomingMessageTV = v.findViewById(R.id.textview_message);
            mViewIncomingMessage = v;
   }
}




   @Override
   public int Mesibo_onGetItemViewType(Mesibo.MessageParams messageParams, String s) {

      if (messageParams.isIncoming()) {

       return MesiboRecycleViewHolder.TYPE_INCOMING;

   }
   return MesiboRecycleViewHolder.TYPE_OUTGOING;


   }

   @Override
   public MesiboRecycleViewHolder Mesibo_onCreateViewHolder(ViewGroup viewGroup, int i) {
      if (MesiboRecycleViewHolder.TYPE_INCOMING == type) {
           View v = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.incoming_chat_layout, viewGroup, false);

           return new IncomingMessgaeViewHolder(v);

       }
       return null;

   }

   @Override
   public void Mesibo_onBindViewHolder(MesiboRecycleViewHolder mesiboRecycleViewHolder, int i, boolean b, Mesibo.MessageParams messageParams, Mesibo.MesiboMessage mesiboMessage) {
if (type == MesiboRecycleViewHolder.TYPE_INCOMING) {

   IncomingMessgaeViewHolder IncomingView = (IncomingMessgaeViewHolder) mesiboRecycleViewHolder;
IncomingView.mIncomingMessageTV.setText(Html.fromHtml(mesiboMessage.message));

}

   }

   @Override
   public void Mesibo_oUpdateViewHolder(MesiboRecycleViewHolder mesiboRecycleViewHolder, Mesibo.MesiboMessage mesiboMessage) {

   }

   @Override
   public void Mesibo_onViewRecycled(MesiboRecycleViewHolder mesiboRecycleViewHolder) {

   }
}
```


## 2. Creating your Activity 
This activity loads the custom `MessagingUIFragment` that is created in the previous step. You need to start this activity when you want to load your chat.

For this, we will be creating an XML file called `activity_messaging.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   xmlns:tools="http://schemas.android.com/tools"
   android:id="@+id/chat_root_layout"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   android:fitsSystemWindows="true"
   android:theme="@style/MesiboAppTheme"
   >

   <android.support.design.widget.AppBarLayout
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:theme="@style/AppTheme.AppBarOverlay">    
   </android.support.design.widget.AppBarLayout>
   
   <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:id="@+id/fragment_container"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       app:layout_behavior="@string/appbar_scrolling_view_behavior" />

   <!-- <include layout="@layout/chat_box_new" /> -->
</android.support.design.widget.CoordinatorLayout>
```


Create  `MessagingActivity` and get the peer(destination) through intent. Mesibo MesiboMessagingFragment requires the peer value to load the chat UI for that user. This activity is called from the segment where you need to launch your own customized message UI. 

```java

public class MessagingActivity extends AppCompatActivity implements MesiboMessagingFragment.FragmentListener {


   MessagingUIFragment mFragment = null;
   private Mesibo.MessageParams mParameter=null;
private Mesibo.UserProfile mUser=null;


   @Override
   protected void onCreate(Bundle savedInstanceState) {
       //getWindow().requestFeature(Window.FEATURE_ACTION_MODE_OVERLAY);

       super.onCreate(savedInstanceState);
       Bundle args = getIntent().getExtras();
       if(null == args) {
           return;
       }

       //TBD, this must be fixed
       if(!Mesibo.isReady()) {
           finish();
           return;
       }

       //Getting peer value from intent
       String peer = args.getString("peer");
    
       mUser = Mesibo.getUserProfile(peer);

       if(null == mUser) {
           mUser = new Mesibo.UserProfile();
           mUser.address = peer;
           mUser.name = peer;
           Mesibo.setUserProfile(mUser, false);
       }

       mParameter = new Mesibo.MessageParams(peer, groupId, Mesibo.FLAG_DEFAULT, 0);

       setContentView(R.layout.activity_messaging);
       startFragment(savedInstanceState);

   }
   
   // Loading your custom fragment
   private void startFragment(Bundle savedInstanceState) {
       // However, if we're being restored from a previous state,
       // then we don't need to do anything and should return or else
       // we could end up with overlapping fragments.
       if (findViewById(R.id.fragment_container) == null || savedInstanceState != null) {
           return;
       }

       // Create a new Fragment to be placed in the activity layout
       mFragment = new MessagingUIFragment();

       // In case this activity was started with special instructions from an
       // Intent, pass the Intent's extras to the fragment as arguments
       mFragment.setArguments(getIntent().getExtras());

       // Add the fragment to the 'fragment_container' FrameLayout
       getSupportFragmentManager().beginTransaction()
               .add(R.id.fragment_container, mFragment).commit();
   }
}

```
### Run your custom view
Now if you need to see your custom UI you need to run the `MessagingActivity` for your custom view.

```java
Intent i = new Intent(this,MessagingActivity.class);
        i.putExtra("peer","Your Destination");
         startActivity(i);
```        

This is just one sample of the customization, Mesibo provides you option to customize almost everything based on your requirement. If you face any issue or if you have any query please write to us.



