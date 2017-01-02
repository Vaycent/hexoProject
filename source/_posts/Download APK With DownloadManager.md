---
title: Download APK With DownloadManager
date: 2016-12-30
layout:
tags: 
- Android
- NetworkRequest
- DownloadManager
thumbnail:
---

This demo will show you how to use a simple way(By DownloadManager) to download an apk, and swith to the installation activity after download completed.
<!--more-->

## DownloadManager Introduction

The download manager is a system service that handles long-running HTTP downloads. Clients may request that a URI be downloaded to a particular destination file. The download manager will conduct the download in the background.

* Use a background service to start DownloadManager
* Use a BroadcastReceiver to receive the download completed intent action
* Add service and receiver declare in AndroidManifest.xml


### Use a background service to start DownloadManager

```
public class DownLoadManagerIntentService extends IntentService {

    private String downloadUrl = "";
    private final String fileName = "vayTest.apk";


    public DownLoadManagerIntentService() {
        super("DownLoadManagerIntentService");
    }

    @Override
    public void onCreate() {
        mlog.e("DownLoadManagerIntentService onCreate");
        super.onCreate();
    }

    @Override
    public void onStart(Intent intent, int startId) {
        mlog.e("DownLoadManagerIntentService onStart");
        super.onStart(intent,startId);
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId){
        mlog.e("DownLoadManagerIntentService onStartCommand");
        downloadUrl = intent.getStringExtra("downloadurl");
        return super.onStartCommand(intent,flags,startId);
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        mlog.e("DownLoadManagerIntentService onHandleIntent");
        download(this,fileName,downloadUrl);
    }

    @Override
    public void onDestroy(){
        mlog.e("DownLoadManagerIntentService onDestroy");
        super.onDestroy();
    }


    private void download(Context context, String name, String apkUrl) {
        DownloadManager downloadManager = (DownloadManager) context.getSystemService(context.DOWNLOAD_SERVICE);
        String dir = isFolderExist("download");
        File f = new File(name);
        if (f.exists())
            f.delete();
        DownloadManager.Request request = new DownloadManager.Request(
                Uri.parse(apkUrl));
        request.setDestinationInExternalPublicDir("download", name + ".apk");
        request.setTitle("App download");
        request.setDescription("\"" + name + "\"now is downloading");
        request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
        try {
            long downloadId = downloadManager.enqueue(request);
        } catch (IllegalArgumentException e) {
            //open by brower
            Intent intent = new Intent();
            intent.setAction("android.intent.action.VIEW");
            Uri content_url = Uri.parse(apkUrl);
            intent.setData(content_url);
            context.startActivity(intent);
        }
    }

    private String isFolderExist(String dir) {
        File folder = Environment.getExternalStoragePublicDirectory(dir);
        boolean rs = (folder.exists() && folder.isDirectory()) ? true : folder
                .mkdirs();
        return folder.getAbsolutePath();
    }
}

```

### Use a BroadcastReceiver to receive the download completed intent action


```
public class DownloadManagerReceiver extends BroadcastReceiver {

    private Context mContext;

    @Override
    public void onReceive(Context context, Intent intent) {
        mContext = context;
        DownloadManager dm = (DownloadManager) mContext
                .getSystemService(mContext.DOWNLOAD_SERVICE);

        if (intent.getAction().equals(DownloadManager.ACTION_DOWNLOAD_COMPLETE)) {
            long downId = intent.getLongExtra(
                    DownloadManager.EXTRA_DOWNLOAD_ID, -1);
            Cursor c = dm.query(new DownloadManager.Query().setFilterById(downId));
            if (c.moveToFirst()) {
                int status = c.getInt(c.getColumnIndex(DownloadManager.COLUMN_STATUS));
                if (status == DownloadManager.STATUS_SUCCESSFUL) {
                    downCompleted(c.getString(c.getColumnIndex(DownloadManager.COLUMN_LOCAL_FILENAME))) ;
                } else {
                    int reason = c.getInt(c.getColumnIndex(DownloadManager.COLUMN_REASON));
                }
            }
        }
        if (intent.getAction().equals(DownloadManager.ACTION_NOTIFICATION_CLICKED)) {
            long[] ids = intent.getLongArrayExtra(DownloadManager.EXTRA_NOTIFICATION_CLICK_DOWNLOAD_IDS);
            dm.remove(ids);
            Toast.makeText(mContext,"Cancel this download",Toast.LENGTH_SHORT).show();
        }
    }
    private void downCompleted(String filePath) {
        File _file = new File(filePath.indexOf("file://")!=-1?filePath.substring(filePath.indexOf("://")+3):filePath);
        Intent intent = new Intent();
        intent.setAction("android.intent.action.VIEW");
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        intent.addCategory("android.intent.category.DEFAULT");
        Uri abc = Uri.fromFile(_file);
        intent.setDataAndType(abc, "application/vnd.android.package-archive");
        mContext.startActivity(intent);
    }
}
```

### Add service and receiver declare in AndroidManifest.xml


```
<service android:name=".DemoPackage.DownloadManagerPackage.DownLoadManagerIntentService"/>


<receiver android:name=".DemoPackage.DownloadManagerPackage.DownloadManagerReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE" />
                <action android:name="android.intent.action.DOWNLOAD_NOTIFICATION_CLICKED"/>
            </intent-filter>
</receiver>
```
