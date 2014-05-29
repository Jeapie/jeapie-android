Own Push handler
================

1. Create class extend from <b>com.jeapie.GCMReceiver</b> and override method 

```java
public class OwnGcmReceiver extends GCMReceiver
{
    public void customActionOnPush(Context context, Intent intent)
    {
        // If need standart action
        super.customActionOnPush(context, intent);

        // your own action
        Log.d("JeapieApi", "push");
    }
}
```
2. In manifest replace line from 

```xml
<receiver android:name="com.jeapie.GCMReceiver">
```
to
```xml
<receiver android:name=".OwnGcmReceiver">
```
