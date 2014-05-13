jeapie-android
==============

> <b>WARNING!</b> Jeapie sdk only for <b>Android version 14+</b>

#### Instructions for install Jeapie sdk

> Please, before you install sdk read documentation about android push notification:
> http://developer.android.com/google/gcm/gcm.html
> http://developer.android.com/google/gcm/gs.html
>
> Also you need to understand the programming language <b>Java</b> and <b>Android</b> development

##### First part (Get android keys)

* You must take GCM sender ID (<b>GCM_SENDER_ID</p>) and <b>GCM API KEY</b> from https://cloud.google.com/console

> Detail information look on http://developer.android.com/google/gcm/gs.html

##### Second part (Create account in Jeapie)


* You must register in service Jeapie https://app.jeapie.com/register
* Copy your <b>APP_KEY</b> and <b>APP_SECRET</b> from Settings -> Api keys
* Copy file jeapie_sdk_v*.*.jar into dir libs in android project
* In Setting -> Push settings you need paste your <b>GCM API KEY</b>

##### Third part (Setting Jeapie in Android project)

* In build.gradle add dependencies
  * Jeapie *.jar lib
  * Play-services 4.+

```ruby
dependencies {
    compile files('libs/jeapie_sdk_v*.*.jar')
    compile 'com.google.android.gms:play-services:4.+'
}
```
> For <b>Eclipse</b> please look in android docs http://developer.android.com/google/play-services/setup.html


* Add to manifest permissions and replace <b>YOUR_PACKAGE_NAME</b> (example com.example.app) in fourth places

```xml
<manifest package="YOUR_PACKAGE_NAME" ...>

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>

    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE"
        android:protectionLevel="signature" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />

    <uses-sdk android:minSdkVersion="14"/>
    <application ...>
        <receiver android:name="com.jeapie.GCMReceiver">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <category android:name="YOUR_PACKAGE_NAME"/>
            </intent-filter>
        </receiver>
        <service android:name="com.jeapie.EventsSenderService"/>
    </application>

</manifest>
```

* In launch activity add next code and replace GCM_SENDER_ID, JEAPIE_APP_KEY, JEAPIE_APP_SECRET on your value

```java

    // GCM Sender ID
    private final static String SENDER_ID = "GCM_SENDER_ID";
    private final static String TAG = "example";
    NotificationManager mNotificationManager;
    private GoogleCloudMessaging mGcm;
    
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // init Jeapie service
        JeapieAPI.init(this, "JEAPIE_APP_KEY", "JEAPIE_APP_SECRET");
        
        // register push handler for handling push notifications
        JeapieAPI.getInstance().registerPushListener(new Pusher());

        // Init GCM service
        mGcm = GoogleCloudMessaging.getInstance(getApplicationContext());

        // Get GCM token asynchronously in background
        registerBackground();

        setContentView(R.layout.main);
    }
    
    // implementation push handler for handling push notification
    // Android push notification is only text information from server. 
    // Text is not displaing for user. You must use "sendNotification" method
    class Pusher implements PushListener {
        @Override
        public void onPush(Intent intent)
        {
            // Get text from push notification with key "message"
            String message = intent.getExtras().getString("message");

            if (message != null) {
                // Display text for user
                sendNotification(message);
            }
        }
    }
    
    // Put the message into a notification and post it.
    // This is just one simple example of what you might choose to do with
    // a GCM message.
    private void sendNotification(String msg) {
        mNotificationManager = (NotificationManager)
                this.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(this, 0,
                new Intent(this, MyActivity.class), 0);

        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(this)
                        .setSmallIcon(R.drawable.common_signin_btn_icon_dark)
                        .setContentTitle("GCM Notification")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify("NOTIFICATION_ID", mBuilder.build());
    }
    
    
    /**
     * Registers the application with GCM servers asynchronously.
     * <p>
     * Stores the registration ID and app versionCode in the application's
     * shared preferences.
     */
    private void registerBackground() {
        new AsyncTask() {
            @Override
            protected Object doInBackground(Object[] objects) {
                String msg = "";
                try {
                    if (mGcm == null) {
                        mGcm = GoogleCloudMessaging.getInstance(getApplicationContext());
                    }
                    String regid = mGcm.register(SENDER_ID);
                    msg = "Device registered, registration id=" + regid;

                } catch (IOException ex) {
                    msg = "Error :" + ex.getMessage();
                }
                return msg;
            }
        }.execute(null, null, null);
    }
```
> For detail information look on http://developer.android.com/google/gcm/client.html
