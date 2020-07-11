---
title: Write your First mesibo Enabled Application
description: Write your First mesibo Enabled Application
keywords: mesibo, android, ios
---
{% include_relative nav.html selected="files" %}

In this part, we will describe file transfer.

## Prerequisites
You MUST go through following prerequisites before you read further.

- Read the [Preparation]({{ '/documentation/tutorials/first-app/' | relative_url }}) Guide.
- Read one of the Android, iOS, JS section of this tutorial.

## How to add file handling

Just like text messages, mesibo allows you to send and receive any arbitrary file (image, audio, video, doc, etc) in real-time. Sending and receiving file is no different from sending and receiving text messages. All an application has to do is to first upload the file to a server and then send the URL and thumbnail [optional] using mesibo in real-time. The receiver then downloads it using that URL. This out-of-band mechanism ensures that no real-time messages are blocked waiting for large files to be uploaded or downloaded.

mesibo offers you the flexibility to store all files on your own servers including private servers or cloud services like Amazon Web Services, Google Cloud Storage, Microsoft Azure etc.

You can use any data structure (for example, json) to send rich messages which contain text message, title, picture url etc. Mesibo provides a convenient utility function sendFile, which does most of the work and allows you to send various other information along with the file such as title, message, geolocation, etc. Receiver is notified of incoming file in listener function `mesibo_onFile()`

To start a file transfer (download or upload), you must initialize a `FileInfo` object and pass it to `sendFile` API. However, instead of creating this object manually, mesibo provides `getFileInstance()` API, which checks if an existing file transfer for this particular URL (download) or the file (upload) is in progress and if so, returns that object instead of creating a new object. This highly optimizes sending a file by avoiding duplicate file transfers.

However, Mesibo does not know or mandate how and where you store your files. Hence in order to use sendFile function, you must assist mesibo in uploading and downloading files to your server. You can achieve that by implementing upload and download handler functions in `FileTransferHandler`, which is called by mesibo real-time API whenever it needs to upload or download file.  For more information, refer to `FileTransferHandler` in the [File Transfer API reference](https://mesibo.com/documentation/api/real-time-api/file-transfer/)

![]({{ '/documentation/api/real-time-api/images/file-handler.png' | relative_url }})

## Sending a file with Mesibo

Here are a brief set of steps you need to follow, for sending a file with mesibo on Android. We will go through each step, along with a code snippet that you can refer to as an example. You can refer the Sample App code on GitHub for a complete understanding.

- For Android, you can refer to [MesiboFileTransferHelper.java](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java) and [SampleAPI.java](https://github.com/mesibo/messenger-app-android/blob/8f40a1005e131442240963ab168f7ce260b62dfa/app/src/main/java/org/mesibo/messenger/SampleAPI.java#L666) 
- Fo iOS, you can refer to [SampleAppFileTransferHandler.m](https://github.com/mesibo/messenger-app-ios/blob/e5af8db4061b54e135f82a2cc39549dae39494a1/MesiboApplication/SampleAppFileTransferHandler.m) and [SampleAPI.m](https://github.com/mesibo/messenger-app-ios/blob/f89f477c687a6143b643402c70f4d61623bf2369/MesiboApplication/SampleAPI.m) 

1. To send a file, create a file object using `getFileInstance`. To create the file instance pass the mode as upload and provide the path to your file. Then call `sendFile()`, to send the file to the recipient. 

The `sendFile` function takes the following parameters:

- **params**, `MessageParams` object initialized with required parameters
- **mid**, Message-ID. For sending messages, Message-ID should be specified in the function parameters instead of using it from params. 
- **file**, `FileInfo` object initialized with file information like path, type, etc. 

Here is an [example in Android](https://github.com/mesibo/ui-modules-android/blob/72d41c5d28f5d23218f1ee77daa17a2fff86e818/Messaging/messaging/src/main/java/com/mesibo/messaging/MessagingFragment.java#L1601)

```java
        Mesibo.FileInfo file = Mesibo.getFileInstance(mParameter, mId, Mesibo.FileInfo.MODE_UPLOAD, filetype, Mesibo.FileInfo.SOURCE_MESSAGE, filePath, null, this);
        file.message = caption;
        file.image = bmp;
        file.title = null;
        file.userInteraction = true;

        m.setFile(file);

        int sendFileresult  = Mesibo.sendFile(mParameter, mId, file);
```

Here is an [example in iOS](https://github.com/mesibo/ui-modules-ios/blob/9b63cb52813082f4f55354513fcf4c929a434865/Messaging/Messaging/MesiboMessageViewController.m#L987)

```objective-c
        MesiboFileInfo *mf = [MesiboInstance getFileInstance:mMesiboParam msgid:m.mid mode:MESIBO_FILEMODE_UPLOAD type:picker.fileType source:MESIBO_FILESOURCE_MESSAGE filePath:picker.mp4Path?picker.mp4Path:picker.filePath url:nil listener:self];
        mf.mid= m.mid;
        mf.message = picker.message;
        //mf.filePath = c.mImagePath;
        mf.image = picker.image;
        mf.asset = picker.phasset;
        mf.localIdentifier = picker.localIdentifier;
        //[mf setPath:picker.filePath];
        mf.userInteraction = YES;
        
        MesiboInstance sendFile:mesiboParam msgid:(u_int32_t)c.mMessageID file:mf];
```
Here is an [example in javascript](https://github.com/mesibo/samples/blob/58b9cc3ef028f6641dc578a09c3aee37e3eaeec3/js/basic-demo/mesibo-demo.js#L139). Here, you need to set the `fileurl` by uploading your file to your server and then getting the location of the file. Refer to the sample code [here](https://github.com/mesibo/messenger-javascript/blob/ad1f9e8d051c94a818c467b2cc0ecf5355bd1989/mesibo/files.js#L196) which does this in javascript. 

```javascript
function sendFile() {
	var p = {};
	p.peer = demo_destination;	
	p.type = 0;
	p.expiry = 3600*24;
	p.flag = MESIBO_FLAG_DELIVERYRECEIPT|MESIBO_FLAG_READRECEIPT;
	
	var msg = {}; //create a rich message
	msg.filetype = 1; // 1 for image 2 for video, 3 audio, 10 other
	msg.size = 1023;	
	msg.fileurl = 'https://cdn.pixabay.com/photo/2019/08/02/09/39/mugunghwa-4379251_1280.jpg'
	msg.title = 'Himalaya';
	msg.message = 'Hello from js';
	
	api.sendFile(p, api.random(), msg);
}
```

2. Now, mesibo has got the path to your file. You want to upload the file to your server, get the url location where the file can be accessed on the server and send that url in a message to the recipient. So that on the other end, your recipient can access that file url, download it and view the file.

But, mesibo does not know where or how to upload it! For this you need to help mesibo by implementing the `FileTransferHandler`
 
`FileTransferHandler` contains functions which will be called by mesibo when you start a file transfer or stop a file transfer. 
	- The function `Mesibo_onStartFileTransfer` is called when you start a file transfer.
	- The function `Mesibo_onStopFileTransfer` is called when you stop a file transfer.

When you call `sendFile`, with the upload mode set in the file instance, mesibo calls `Mesibo_onStartFileTransfer`.

3. Here is an example of [Mesibo_onStartFileTransfer](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L220) in Android
```java
@Override
    public boolean Mesibo_onStartFileTransfer(Mesibo.FileInfo file) {
        if(Mesibo.FileInfo.MODE_DOWNLOAD == file.mode)
            return Mesibo_onStartDownload(file.getParams(), file);

        return Mesibo_onStartUpload(file.getParams(), file);
    }
``` 
Here is an example of [Mesibo_onStartFileTransfer](https://github.com/mesibo/messenger-app-ios/blob/e5af8db4061b54e135f82a2cc39549dae39494a1/MesiboApplication/SampleAppFileTransferHandler.m#L125) in iOS.
```objective-c
-(BOOL) Mesibo_onStartFileTransfer:(MesiboFileInfo *)file {
    
    if(MESIBO_FILEMODE_DOWNLOAD == file.mode) {
        return [self Mesibo_onStartDownload:file];
    }
    
    return [self Mesibo_onStartUpload:file];    
}
```
For upload mode, `Mesibo_onStartUpload` is called which is your upload handler.
For download mode, `Mesibo_onStartDownload` is called which is your download handler.
  
4. In `Mesibo_onStartUpload`, you make an HTTP request to upload your file to the server. 

- For Android, Refer to the sample code [here](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L146) 
- For iOS, Refer to the sample code [here](https://github.com/mesibo/messenger-app-ios/blob/e5af8db4061b54e135f82a2cc39549dae39494a1/MesiboApplication/SampleAppFileTransferHandler.m#L19)

As the file is uploading, the status of the file uploaded will be updated by `updateFileTransferProgress`. The listener `onFileTransferProgress` if set will be called with this progress. You can read this and update your UI accordingly, for example to display a spinner or a progress bar to show that the file is uploading.

Once your upload is complete, you get the fileurl. The url parameter is set in the file object and your file is sent.

5. On the recepient end, when a file is received, the listener `Mesibo_onFile` is called with the `FileInfo` object. Mesibo understands that this file needs to be downloaded to view it. So, the file download mode is set and `Mesibo_onStartFileTransfer` is called, which then calls the download handler.  

The download handler reads the file data from the url present in the `FileInfo` object received. In `Mesibo_onStartDownload`, you need to download the file from the server and read the contents.  

- For Android, Refer to the sample code [here](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L146). 
- For iOS, Refer to the sample code [here](https://github.com/mesibo/messenger-app-ios/blob/e5af8db4061b54e135f82a2cc39549dae39494a1/MesiboApplication/SampleAppFileTransferHandler.m#L83)

Here, the file is downloaded from the server located at the file url and then stored at a specified path.

As the file is downloading, the status of the file downloaded will be updated by `updateFileTransferProgress`. The listener `onFileTransferProgress` if set will be called with this progress. You can read this and update your UI accordingly, for example to display a spinner or a progress bar to show that the file is downloading.

In the sample upload and download handlers, the [Mesibo HTTP Library](https://mesibo.com/documentation/api/http-library/) is being used to handle HTTP requests. However, you can also use your own methods to handle HTTP requests and responses instead of using the Mesibo HTTP Library. 

If you have any questions, checkout the [File Handling FAQ](https://mesibo.com/documentation/faq/file-handling)

[On to Part 9 >>](push-notification.md){: class="button outline-btn" style="margin-bottom: 30px; margin-right: 100%"}
