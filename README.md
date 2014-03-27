jeapie-android
==============

#### Instructions for install Jeapie sdk

* You must register in service Jeapie https://app.jeapie.com/register
* Copy your app_key and app_secret from Settings -> Api keys
* Copy file jeapie_sdk_v*.*.jar into dir libs in android project
* In build.gradle add dependencies

```ruby
dependencies {
    compile files('libs/jeapie_sdk_v*.*.jar')
    compile 'com.google.android.gms:play-services:4.+'
}
```
* Add to manifest permissions

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
* In launch activity add

```java

    private final statis String SENDER_ID = "GCM_SENDER_ID";
    private final statis String TAG = "example";
    NotificationManager mNotificationManager;
    private GoogleCloudMessaging mGcm;
    
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // init Jeapie service
        JeapieAPI.init(this, "JEAPIE_API_KEY", "JEAPIE_API_SECRET");
        // register push handler
        JeapieAPI.getInstance().registerPushListener(new Pusher());

        mGcm = GoogleCloudMessaging.getInstance(getApplicationContext());

        registerBackground();

        setContentView(R.layout.main);
    }
    
    // implementation push handler 
    class Pusher implements PushListener {
        @Override
        public void onPush(Intent intent)
        {
            Log.d(TAG, "Handle intent");

            GoogleCloudMessaging gcm = GoogleCloudMessaging.getInstance(getApplicationContext());

            String message = intent.getExtras().getString("message");

            if (message != null) {
                Log.d(TAG, message);
                sendNotification(message);
            }
        }
    }
    
    // show push in device
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
    
    
    // register gcm token
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

Detail information about GCM on http://developer.android.com/google/gcm/client.html
