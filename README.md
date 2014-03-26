jeapie-android
==============

#### Instructions for install Jeapie sdk

1. You must register in service Jeapie https://app.jeapie.com/register
2. Copy your app_key and app_secret from Settings -> Api keys
3. Copy file jeapie_sdk_v*.*.jar into dir libs in android project
4. Add to manifest

```xml
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<permission android:name="YOUR_PROJECT_PACKEGE.permission.C2D_MESSAGE" android:protectionLevel="signature" />
<uses-permission android:name="com.example.jeapie_test.permission.C2D_MESSAGE" />
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
<uses-permission android:name="android.permission.WAKE_LOCK"/>
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
```
