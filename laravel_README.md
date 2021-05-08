## mesibo Laravel Demo
This folder contains the source code for the mesibo Laravel Demo.

### Prerequisites
- Installed Laravel by following the instructions [here](https://laravel.com/docs/)
- Downloaded the source code for the Laravel Basic demo app from [samples](https://github.com/mesibo/samples/tree/master/laravel) available on mesibo Github repo 
- Read the [Get Started Guide]({{ 'https:/mesibo.com/documentation/tutorials/get-started/') to learn the basics of using mesibo, creating apps, creating users, etc.
- The Laravel demo uses mesibo Javascript API. Refer to [Get Started - Javascript]('http://mesibo.com/documentation/tutorials/get-started/javascript/') to learn how to create apps using mesibo in Javascript 
- Basic knowledge of building apps on Laravel 

## Running the Laravel Demo App
1. Create two mesibo users. Refer to the tutorial [Create Users]('https://mesibo.com/documentation/tutorials/get-started/create-users/')
2. Open the folder `resources/views`. You will find two files - `app-user-1.blade.php` and `app-user-2.blade.php`. Here, fill in the token and appid for the two users that you created earlier. 
3. For example, say you created two users with addresses `123` and `456`.
Configure the token and appid for the first user `123` in `app-user-1.blade.php` as follows:
```html
<script type="text/javascript">
  var MESIBO_DEMO_USER_CONFIG = {
    'token':'xxxxx----token-for-user-1--xxxxxxxxxxx',
    'appid': 'com.mesibo.firstapp',
    'destination':'456'
  };
</script>
```
Similarly, configure the second user `456` in `app-user-2.blade.php`
```html
<script type="text/javascript">
  var MESIBO_DEMO_USER_CONFIG = {
    'token':'xxxxx----token-for-user-2--xxxxxxxxxxx',
    'appid': 'com.mesibo.firstapp',
    'destination':'123'
  };
</script>
```
4. Once you have configured both the users, serve the app using the following command:
```bash
php artisan serve -port=8080
```

5. Open `http://127.0.0.1:8080/` on your browser and click on the button `Login as User-1`. 
6. Open `http://127.0.0.1:8080/` on an another browser window(incognito) and click on the button `Login as User-2`. 
7. These users will now be able to send messages and make calls to each other.
