---
title: Android 7.1 Shortcuts Guide
tags: 
- Android
- Android 7.1
- Shortcuts
---


Shortcuts is a new feature in Android 7.1, you can have a long press with the app icon on your launcher to call it out. If your app targets Android 7.1 (API level 25) or higher, you can define shortcuts to specific actions in your app. These shortcuts can be displayed in a supported launcher. Shortcuts let your users quickly start common or recommended tasks within your app.
<!--more-->


## Two different types of shortcuts
1. **Static Shortcuts**  
Static shortcuts are defined in a resource file that is packaged into an APK. Therefore, you must wait until you update your entire app to change the details of these static shortcuts.
2. **Dynamic Shortcuts**  
Dynamic shortcuts are published at runtime using the ShortcutManager API. During runtime, your app can publish, update, and remove its dynamic shortcuts.

![shortcuts pic](https://raw.githubusercontent.com/Vaycent/hexoIndigoTheme/master/MyBlogMaterial/2016/android7.1_shortcuts00.png)

## Using Static Shortcuts
* Found out the main activity in your AndroidManifest.xml, add a <meta-data>element with shortcuts to this resource file

```
 <activity
            android:name=".MainActivity"
            android:configChanges="orientation"
            android:label="@string/app_name"
            android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <!--*** shortuts ***-->
            <meta-data android:name="android.app.shortcuts"
                android:resource="@xml/shortcuts" />
        </activity>
```

* Crrate a new resource file(res/xml/shortcuts.xml)

```
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">

    <shortcut
        android:shortcutId="id_shortcut1"
        android:enabled="true"
        android:icon="@drawable/ic_face_black_24dp"
        android:shortcutShortLabel="@string/shortcut_shortto_volleysharp"
        android:shortcutLongLabel="@string/shortcut_longto_volleysharp"
        android:shortcutDisabledMessage="@string/shortcun_disabled">
        <intent
            android:action="ShortCut To VolleysharpDemo"
            android:targetClass="vaycent.vaycentproject.DemoPackage.VolleysharpDemo"
            android:targetPackage="vaycent.vaycentproject"  />
    </shortcut>

    <shortcut
        android:shortcutId="id_shortcut2"
        android:enabled="true"
        android:icon="@drawable/ic_android_black_24dp"
        android:shortcutShortLabel="@string/shortcut_shortto_ormliteSharp"
        android:shortcutLongLabel="@string/shortcut_longto_ormliteSharp"
        android:shortcutDisabledMessage="@string/shortcun_disabled"
        >
        <intent
            android:action="ShortCut To OrmliteSharpDemo"
            android:targetClass="vaycent.vaycentproject.DemoPackage.OrmliteSharpDemo"
            android:targetPackage="vaycent.vaycentproject" />
    </shortcut>
</shortcuts>
```



## Using Dynamic Shortcuts

### Add a new dynamic shortcuts

```
private void AddShortcutEvent(){
        ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);

        ShortcutInfo shortcut = new ShortcutInfo.Builder(this, "id1")
                .setShortLabel("Web site")
                .setLongLabel("Open My Web Site")
                .setIcon(Icon.createWithResource(this, R.drawable.ic_face_black_24dp))
                .setIntent(new Intent(Intent.ACTION_VIEW,
                        Uri.parse("https://vaycent.github.io")))
//                .setRank(1) //This can change the shortcuts sorting
                .build();

        ShortcutInfo dynamicShortcut2 = new ShortcutInfo.Builder(this, "id2")
                .setShortLabel("Dynamic Shortcut")
                .setLongLabel("Open Dynamic shortcut")
                .setIcon(Icon.createWithResource(this, R.drawable.ic_face_black_24dp))
                .setIntents(
                        new Intent[]{
                                new Intent(Intent.ACTION_MAIN, Uri.EMPTY, this, ShortcutsDemo.class)
                                        .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK)
                        })
                .build();
        shortcutManager.setDynamicShortcuts(Arrays.asList(shortcut,dynamicShortcut2));

    }
```

### Update the dynamic shortcuts

```
private void UpdateShortcutEvent(){
        ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);

        ShortcutInfo shortcut = new ShortcutInfo.Builder(this, "id1")
                .setShortLabel("Baidu")
                .setLongLabel("Open Baidu Web")
                .setIcon(Icon.createWithResource(this, R.drawable.ic_face_black_24dp))
                .setIntent(new Intent(Intent.ACTION_VIEW,
                        Uri.parse("https://www.baidu.com")))
                .build();

        shortcutManager.updateShortcuts(Arrays.asList(shortcut));
    }
```

### Remove the dynamic shortcuts

```
private void RemoveId1ShortcutEvent(){
        ShortcutManager mShortcutManager = getSystemService(ShortcutManager.class);
        List<ShortcutInfo> infos = mShortcutManager.getPinnedShortcuts();
        for (ShortcutInfo info : infos) {
            if (info.getId().equals("id" + 1)) {
                mShortcutManager.disableShortcuts(Arrays.asList(info.getId()), "No this id");
            }
        }
        mShortcutManager.removeDynamicShortcuts(Arrays.asList("id" + 1));
    }

    private void RemoveAllShortcutEvent(){
        ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);
        shortcutManager.removeAllDynamicShortcuts();
    }
```

### Get the static or dynamic shortcuts

```
List<ShortcutInfo> staticInfos=shortcutManager.getManifestShortcuts();
List<ShortcutInfo> dynamicInfos=shortcutManager.getDynamicShortcuts();
```