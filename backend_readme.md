## Backend for Conferencing demo

This repository contains the backend source code for the Mesibo Conferencing Demo app

The mesibo sample conferencing app hosted at https://mesibo.com/livedemo and is a fully functional, Zoom Like Video Conferencing app which you can directly integrated into your website.

## Features:
- Group Voice and Video Call with unlimited members
- Live Streaming
- Screen Sharing
- Fine control over all video & audio parameters and user permissions
- Supports video streaming at various resolutions: Standard, HD, FHD and 4K
- Group Chat
- One-to-One chat
- Invite Participants

### Configuring the backend
For the purpose of the demo app we will implement various functionality like user authentication, creating the room, etc. To do this we need to setup a backend. If you do not want go about setting up your own backend, you can use mesibo's private APIs hosted on `https://app.mesibo.com/conf/api.php`. 

Modify `MESIBO_API_BACKEND` in [config.js](https://github.com/mesibo/conferencing/blob/master/live-demo/web/mesibo/config.js) accordingly to point to your own URL once your backend is setup.

### Modify config.php
To setup Mesibo backend modify the file [config.php](https://github.com/mesibo/conferencing/blob/master/live-demo/backend/config.php)

```php
<?php

$db_host = "localhost";
$db_name  = "confdemo";         // the name of the database to connect to
$db_user  = "confuser";           // the username to connect with
$db_pass  = "confpass";      // the user's password

// OBTAINED BY SIGNING AT mesibo.com
$apikey = "<get yours from https://mesibo.com/console >";
$apptoken = "<get yours from https://mesibo.com/console >";

// google recaptcha secret [optional]
$captcha_secret = '';
```
Follow the steps below to get API Key and APP token.

1. Login to [Mesibo console](https://mesibo.com/console). If you haven't already created an application create a new application.
![Application](https://mesibo.com/documentation/tutorials/first-app/images/app-token.jpg)
2. Once your application is created, note down the App token. The App Token looks like the following:
`cn9cvk6gnm15e7lrjb2k7ggggax5h90n5x7dp4sam6kwitl2hmg4cmwabet4zgdw`
3. Click on the settings icon to get the API key
