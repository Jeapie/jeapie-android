Getting started
===============

#### First part (Get android keys)

* You have to take GCM sender ID (<b>GCM_SENDER_ID</p>) and <b>GCM API KEY</b> from https://cloud.google.com/console

> For more detailed information visit http://developer.android.com/google/gcm/gs.html

#### Second part (Create account in Jeapie)


* You should register in Jeapie service  https://app.jeapie.com/register
* Copy your <b>APP_KEY</b> and <b>APP_SECRET</b> from Settings -> Api keys (In Jeapie Dashboard)
* Copy file jeapie_sdk_v*.*.jar into dir libs in your android project
* In Jeapie dashboard -> Settings -> Push settings you need paste your <b>GCM API KEY</b>

#### Third part (Setting Jeapie in Android project)

* In build.gradle add dependencies
  * Jeapie *.jar lib
  * Play-services 4.+

```ruby
dependencies {
    compile files('libs/jeapie_sdk_v*.*.jar')
    compile 'com.google.android.gms:play-services:4.+'
}
```
> If you are using <b>Eclipse</b> please take a look http://developer.android.com/google/play-services/setup.html


* Add to manifest permissions and replace <b>YOUR_PACKAGE_NAME</b> (example com.example.app) in four places 
and replace JEAPIE_APP_KEY, JEAPIE_APP_SECRET on your value

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
        <meta-data
            android:name="com.jeapie.key"
            android:value="JEAPIE_APP_KEY" />
        <meta-data
            android:name="com.jeapie.secret"
            android:value="JEAPIE_APP_SECRET" />
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

* In launch activity add next code and replace GCM_SENDER_ID on your value

```java

    // GCM Sender ID
    private final static String SENDER_ID = "GCM_SENDER_ID";
    private final static String TAG = "example";
    
    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // init Jeapie service
        JeapieAPI.init(this);
        
        // Check device for Play Services APK.
        if (checkPlayServices()) {
            // Get GCM token asynchronously in background
            JeapieAPI.getInstance().registerTokenInBackground(this, SENDER_ID);
        }

        setContentView(R.layout.main);
    }
    
    /**
     * Check the device to make sure it has the Google Play Services APK. If
     * it doesn't, display a dialog that allows users to download the APK from
     * the Google Play Store or enable it in the device's system settings.
     */
    private boolean checkPlayServices() {
        int resultCode = GooglePlayServicesUtil.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (GooglePlayServicesUtil.isUserRecoverableError(resultCode)) {
                GooglePlayServicesUtil.getErrorDialog(resultCode, this,
                        PLAY_SERVICES_RESOLUTION_REQUEST).show();
            } else {
                Log.i(TAG, "This device is not supported.");
                finish();
            }
            return false;
        }
        return true;
    }
    
```
> For more detailed information visit http://developer.android.com/google/gcm/client.html
