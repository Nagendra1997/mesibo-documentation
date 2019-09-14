
## Using MesiboUI
Mesibo offers ready to use UI modules which be used to build your application . Mesibo UI modules are completely customizable. 

## Mesibo UI Fragments

There are primarily two types of UI fragment
 - `MesiboUserListFragment`, which displays all the users and messages 
 - `MesiboMessagingFragment`, which renders all the messages for a particular user or a group

You can use these fragments in your code as described below.

## MesiboUserListFragment:
`MesiboUserListFragment` displays the list of users and the last message of each user sorted by timestamp. For example, when a typical messaging appliaction(say WhatsApp) starts, a user list is displayed along with the last message for each user.
 
### Using MesiboUserListFragment:
 
Mesibo provides modes that can be passed as argument to the activity, based on which the required fragment can be loaded. 

The different modes for `MesiboUserListFragment` are -

 - `MesiboUI.USERLIST_GROUPSELECTION_MODE` launches UI for selecting and creating a group. 
 
 - `MesiboUI.USERLIST_EDIT_GROUP_MODE` launches UI for editing diiferent attributes of a group such as - 
    Group Member List, Group Profile Picture, Group Title or Group Name. 
 
 - `MesiboUI.USERLIST_FORWARD_MODE` populates a list of users to whom you can forward your message or file. 		
 
 - `MesiboUI.USERLIST_NEWCONTACTS_MODE` displays Mesibo Contact List - a list of Mesibo Users in that application.
 
 - `MesiboUI.USERLIST_MESSAGELIST_MODE` displays list of previous messages or chat history.
 
 Based on these Modes,there are additional arguments that you need to provide such as -
 
 - `MesiboUI.MESSAGE_ID` - To load `MesiboUI.USERLIST_FORWARD_MODE` you need to pass `MesiboUI.MESSAGE_ID`
    where MESSAGE_ID is the unique ID for each message ,that you need to forward.
 
 - `MesiboUI.MESSAGE_IDS`- Multiple messages can  be forwarded at a single time. To load `MesiboUI.USERLIST_FORWARD_MODE` 
    you can provide an array `MesiboUI.MESSAGE_IDS`, which contains all the MESSAGE_IDs of the messages to be forwarded.
 
 - `MesiboUI.MESSAGE_CONTENT`- The contents of the message  
 
 - `MesiboUI.FORWARD_AND_CLOSE` - Use this argument to forward your message to other users and then close the 		      	 `MesiboForwardFragment` after forwarding message is done.


For example,

Load `MesiboUserListFragment` from activity.

### Loading MesiboUI.MESSAGE_LIST_MODE
```java
            MesiboUserListFragment userListFragment = new MesiboUserListFragment();
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
```

### Loading MesiboUI.USERLIST_NEWCONTACTS_MODE
```java
            MesiboUserListFragment userListFragment = new MesiboUserListFragment();
            userListFragment.setListener(this);
            Bundle bl = new Bundle();
            bl.putInt(MesiboUI.USERLIST_NEWCONTACTS_MODE, mMode);
            userListFragment.setArguments(bl);
            FragmentManager fm = getSupportFragmentManager();
            FragmentTransaction ft = fm.beginTransaction();
            ft.replace(R.id.userlist_fragment, userListFragment,"null");
            ft.addToBackStack("userListFragment");
            ft.commit();
```
### Loading MesiboUI.USERLIST_FORWARD_MODE
```java
            MesiboUserListFragment userListFragment = new MesiboUserListFragment();
            userListFragment.setListener(this);
            Bundle bl = new Bundle();
            bl.putInt(MesiboUI.USERLIST_FORWARD_MODE, mMode);
            bl.putLong(MesiboUI.MESSAGE_ID, mForwardId);
	    
            if(!TextUtils.isEmpty(forwardMessage))
                bl.putString(MesiboUI.MESSAGE_CONTENT, forwardMessage);

            bl.putLongArray(MesiboUI.MESSAGE_IDS, mForwardIds);
 
            bl.putBoolean(MesiboUI.FORWARD_AND_CLOSE, forwardAndClose);

            userListFragment.setArguments(bl);
            FragmentManager fm = getSupportFragmentManager();
            FragmentTransaction ft = fm.beginTransaction();
            ft.replace(R.id.userlist_fragment, userListFragment,"null");
            ft.addToBackStack("userListFragment");
            ft.commit();
```
### Loading MesiboUI.USERLIST_EDIT_GROUP_MODE

### Loading MesiboUI.USERLIST_GROUPSELECTION_MODE

For example, if the `Mode` is `MesiboUI.USERLIST_NEWCONTACTS_MODE` and you pass this as an argument to the fragment,
it will load the list of all available contacts. 


## MesiboMessagingFragment :
`MesiboMessagingFragment` renders all the messages for a particular user or a group which you can load in your application as it suits you. 

### Using Mesibo MessagingFragment

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
  

//Mesibo has all modes required by the user to minimize the effort of customizing and development. Even after this if you //like to customize your and have your UI you are welcome to do so. Customization we will see further in this section.



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



