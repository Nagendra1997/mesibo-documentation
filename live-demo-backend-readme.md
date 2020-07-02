## Backend for Sample Conferencing App 

This repository contains the backend source code for the mesibo Sample Conferencing App. 

The demo app uses  Private Backend APIs . The frontend of your app users these private APIs for login and other administrative taskstasks such as creating users, groups,etc. In turn, the private API connects to the mesibo backend API to perform the necessary operations your database. 

You can set up your own backend or you can use mesibo's backend APIs hosted on `https://app.mesibo.com/conf/api.php`. 

If you have set up your own backend modify[config.js](https://github.com/mesibo/conferencing/blob/master/live-demo/web/mesibo/config.js) accordingly to point to your own URL and you are good to go. 

### Hosting Backend APIs
To set up the mesibo backend API for the conferencing app, follow the steps below.

## Prerequisites
Before we dive into setting up the backend for the conferencing app, please ensure the following
- Familiarity with mesibo API. Refer [Getting Started Guide](https://mesibo.com/documentation/get-started/) and [First App Tutorial](https://mesibo.com/documentation/tutorials/first-app/#preparation) if you are not familiar.
- Familiarity with mesibo [User and Group Management Backedn APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis)
- Familiar with the mesibo conferencing and streaming APIs.

Please ensure that the following servers are running.
- A web server with HTTPS and PHP support. Depending on the web server (apache / h2o/ Lighttpd/ Nginx) you are using, the setup varies. In the subsequent examples, we will assume that your hostname is `example.com` and the backend code which you will download soon is accessible via URL `https://example.com/api.php`
- MySQL server

- Get Mesibo API Key and the App token. Refer to [First Mesibo App tutorial](https://mesibo.com/documentation/tutorials/first-app/#preparation) to learn about how to get API key and App token.

### Setting up the Database

You will need to set up a database so that backend can store various information:

- Users email/phone number and login token
- Groups, group members, and group admins

The private APIs uses MySQL as the database. We will not go into details of setting up MySQL server; there are plenty of tutorials on the web if you are not familiar. Once you’ve set up the MySQL server, create a database and the credentials. These credential will be needed by the backend code to access the database.

The next step is to create the database schema using the supplied SQL file `mysql-schema.sql`. Run following to create the database schema for the backend.

```
$ mysql -h <host> -u <username> -p <password> <dbname> < mysql-schema.sql
```

### Configure the backend APIs

We now have all the information to configure and go live with private API on your own servers.

Open `config.php` in the downloaded code and enter all the information we have obtained above, namely:
- MySQL host, database name, username, and password
- The API key and APP token.Refer to [First Mesibo App tutorial](https://mesibo.com/documentation/tutorials/first-app/#preparation) to learn about how to get API key and App token.

**Optional** If you are using Google ReCaptcha set the recaptcha secret token.

Modify the file [config.php](https://github.com/mesibo/conferencing/blob/master/live-demo/backend/config.php)
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
### Testing your Private API
It’s time now to test the backend. Open `https://example.com/api.php` in your browser. It should output
`{ "code": "NOOP", "result": "FAIL" }`

Above output indicates that the backend appears to be set up correctly. 

### Configure the conferencing demo app to use your backend API

Once you are done with the backend setup, Edit the [config.js](https://github.com/mesibo/conferencing/blob/master/live-demo/web/mesibo/config.js) of the app to use your own private API URL (replacing https://app.mesibo.com/api.php with the new one).

- Edit `conferencing/live-demo/web/mesibo/config.js` and enter the private API url in `MESIBO_API_BACKEND` field
- Edit `conferencing/live-demo/web/mesibo/config.js` and enter the grecaptcha public key in `MESIBO_CAPTCHA_TOKEN` field

For example,
```javascript
const MESIBO_API_BACKEND = 'https://example.com/api.php';
const MESIBO_CAPTCHA_TOKEN = '6LceR_sUxxxxxxxxxxxxPSCU-_jcfU';
```

That's it! You are now good to go about running the conferecing demo app connected to your own backend.


