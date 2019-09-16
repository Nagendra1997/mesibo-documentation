
## Using MesiboUI components
Mesibo offers ready to use UI fragments which be used to build your application . Mesibo UI fragments are completely customizable as per your need.  

There are primarily two types of UI fragment
 - `MesiboUserListFragment`, which displays all the users and messages 
 - `MesiboMessagingFragment`, which renders all the messages for a particular user or a group

You can use these fragments in your code as described below.

## MesiboUserListFragment:
`MesiboUserListFragment` displays the list of users and the last message of each user sorted by timestamp. For example, when a typical messaging appliaction(say WhatsApp) starts, a user list is displayed along with the last message for each user.
 
### Using MesiboUserListFragment:
 
MesiboUserListFragment provides modes that can be passed as argument to the activity, based on which the required fragment can be loaded. 

The different modes for `MesiboUserListFragment` are -

 - `MesiboUserListFragment.MODE_SELECTGROUP` launches UI for selecting and creating a group. 
 
 - `MesiboUserListFragment.MODE_EDITGROUP` launches UI for editing diiferent attributes of a group such as - 
    Group Member List, Group Profile Picture, Group Title or Group Name. 
 
 - `MesiboUserListFragment.MODE_SELECTCONTACT_FORWARD` populates a list of users to whom you can forward your message or file. 		
 
 - `MesiboUserListFragment.MODE_SELECTCONTACT` displays Mesibo Contact List - a list of Mesibo Users in that application.
 
 - `MesiboUserListFragment.MODE_MESSAGELIST` displays list of previous messages or chat history.
 



For example,

### Loading Message List
Load list of users and last messages in `MesiboUserListFragment` from a activity. You can start this activity from anywhere to load user list.

```java
public class ExampleActivity extends AppCompatActivity implements MesiboMessageListFragment.FragmentListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
	setContentView(R.layout.activity_messages);
	
                MesiboUserListFragment userListFragment = new MesiboUserListFragment();
                userListFragment.setListener(MessageListActivity.this);
		
                Bundle bl = new Bundle();
                bl.putInt(MesiboUserListFragment.MESSAGE_LIST_MODE, MesiboUserListFragment.MODE_MESSAGELIST);
                userListFragment.setArguments(bl);
		
                FragmentManager fm = getSupportFragmentManager();
                FragmentTransaction ft = fm.beginTransaction();
                ft.replace(R.id.userlist_fragment, userListFragment, "null");
                ft.commit();

        
    }
}
```


<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/message_liist.png" width="320" height="550">

### Loading New Contacts list

Similar to above example, load the list of all contacts just by changing the type of mode. You can start new chat by selecting user in the list.

```java	
            Bundle bl = new Bundle();
            bl.putInt(MesiboUserListFragment.MESSAGE_LIST_MODE, MesiboUserListFragment.MODE_SELECTCONTACT);
            userListFragment.setArguments(bl);
           
```

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/new_contact.png" width="320" height="550">

### Loading Forward List

Forward list loads the list of user whom any message or file can be forwarded. Select user to forward message. It also show recent contacted users. Change the type of mode to `MesiboUserListFragment.MODE_SELECTCONTACT_FORWARD`.

There are some extra arguments to load forward list

- `MesiboUI.MESSAGE_ID`- MESSAGE_ID is the unique ID for each message ,that you need to forward.
- `MesiboUI.MESSAGE_IDS` - Multiple message can also be forwarded at a time by  putting all the message Ids in an array.
- `MesiboUI.FORWARD_AND_CLOSE` - FORWARD_AND_CLOSE is boolean argument. You can close the forward list sson after your message is forwarded. Pass `true' to forward and close forward list.
- `MesiboUI.MESSAGE_CONTENT`- The conatins the content of a message.  


```java	
            Bundle bl = new Bundle();
            bl.putInt(MesiboUserListFragment.MESSAGE_LIST_MODE, MesiboUserListFragment.MODE_SELECTCONTACT_FORWARD);
            bl.putString(MesiboUI.MESSAGE_CONTENT,"This is forward message Test");
            bl.putBoolean(MesiboUI.FORWARD_AND_CLOSE,false);
	    userListFragment.setArguments(bl);
            
```

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/forward_mode.png" width="320" height="550">

### Loading Editing Group 
This loads fragment to edit the group. Members, group name etc can be edited from here

```java	
            Bundle bl = new Bundle();
            bl.putInt(MesiboUserListFragment.MESSAGE_LIST_MODE, MesiboUserListFragment.MODE_EDITGROUP);

            userListFragment.setArguments(bl);
            
```

### Loading MesiboUI.USERLIST_GROUPSELECTION_MODE

This loads fragment to select a group.

```java
             Bundle bl = new Bundle();
             bl.putInt(MesiboUserListFragment.MESSAGE_LIST_MODE, MesiboUserListFragment.MODE_SELECTGROUP);

             userListFragment.setArguments(bl);
            
```


## MesiboMessagingFragment :
`MesiboMessagingFragment` renders all the messages for a particular user or a group which you can load in your application as it suits you. 

### Using MesiboMessagingFragment

MessagingFragment loads chat view when you click on any of the existing chat or start a new chat. Mesibo MessagingFragment renders all messages and populate a recycler view with messages.

Load MesiboMessagingFragment from your activity by passing `MesiboUI.PEER` or `MesiboUI.GROUP_ID` in bundle. Put value of these two parameter in a Bundle. 

- `MesiboUI.PEER` - User Address(Destination).
- `MesiboUI.GROUP_ID` - Group id.

 There are few extra arguments -
 - `MesiboMessagingFragment.READONLY` - This is a boolean argument to hide and show the reply view. Pass `true` if you want to read only not reply.
 
 - `MesiboMessagingFragment.SHOWMISSEDCALLS` - Pass `true` if you want to display alert of missed calls. 
 
 
```java

        MesiboMessagingFragment mFragment = new MesiboMessagingFragment();
	
        Bundle bl = new Bundle();
        bl.putString(MesiboUI.PEER, "John Player");
        bl.putLong(MesiboUI.GROUP_ID,0);
        bl.putLong(MesiboUI.MESSAGE_ID,0);
	bl.putBoolean(MesiboMessagingFragment.READONLY,false);
        bl.putBoolean(MesiboMessagingFragment.SHOWMISSEDCALLS,false);

        mFragment.setArguments(bl);
	
	// Add the fragment to the 'fragment_container' FrameLayout
        getSupportFragmentManager().beginTransaction()
                .add(R.id.fragment_container, mFragment).commit();

```
<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/messaging_fragmentt.png" width="320" height="550">


Mesibo has all modes required by the user to minimize the effort of customizing and development. Even after this if you like to customize your and have your UI you are welcome to do so. Customization we will see next in this section.

## Customization of MesiboMessagingFragment - own chat view

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

### Full MessagingUIFragment code
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
       String peer = args.getString(MesiboUI.PEER);
    
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
        i.putExtra(MesiboUI.PEER,"Your Destination");
         startActivity(i);
```        

This is just one sample of the customization, Mesibo provides you option to customize almost everything based on your requirement. If you face any issue or if you have any query please write to us.




## Mesibo Config

`MesiboUI.Config` gives you option to get config variable and change according to your need. Mesibo.Config can also be written in your desired language. 

For example, If you want to change the title of MesiboMessageList, get instance if Mesibo.Config and set your value in its parameter. 

```java
MesiboUI.Config config = MesiboUI.getConfig();
        config.messageListTitle = "New Messages";
	
```





