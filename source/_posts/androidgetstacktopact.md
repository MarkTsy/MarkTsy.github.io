---
title: Android获取当前栈顶Activity
date: 2020-03-12 20:06:36
tags: [android, 栈顶activity]
categories: Android
---

### 1. Shell

通过dumpsys命令, 获取当前焦点窗口的包名/类名

```shell
dumpsys activity | grep "mFoucsActivty"
```

<!--more-->

### 2. 辅助功能(AccessibilityService)

辅助功能是帮助残障人士使用, 所以能够监听到当前窗口的变化, 需要用户手动开启权限

```java
public class WindowChangeDetectingService extends AccessibilityService {

    @Override
    protected void onServiceConnected() {
        super.onServiceConnected();

        //Configure these here for compatibility with API 13 and below.
        AccessibilityServiceInfo config = new AccessibilityServiceInfo();
        config.eventTypes = AccessibilityEvent.TYPE_WINDOW_STATE_CHANGED;
        config.feedbackType = AccessibilityServiceInfo.FEEDBACK_GENERIC;

        if (Build.VERSION.SDK_INT >= 16)
            //Just in case this helps
            config.flags = AccessibilityServiceInfo.FLAG_INCLUDE_NOT_IMPORTANT_VIEWS;

        setServiceInfo(config);
    }

    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
        if (event.getEventType() == AccessibilityEvent.TYPE_WINDOW_STATE_CHANGED) {
            ComponentName componentName = new ComponentName(
                event.getPackageName().toString(),
                event.getClassName().toString()
            );

            ActivityInfo activityInfo = tryGetActivity(componentName);
            boolean isActivity = activityInfo != null;
            if (isActivity)
                Log.i("CurrentActivity", componentName.flattenToShortString());
        }
    }

    private ActivityInfo tryGetActivity(ComponentName componentName) {
        try {
            return getPackageManager().getActivityInfo(componentName, 0);
        } catch (PackageManager.NameNotFoundException e) {
            return null;
        }
    }

    @Override
    public void onInterrupt() {}
}
```

AndroidManifest.xml

```markup
<application>
    ....    <service android:label="@string/accessibility_service_name" android:name=".WindowChangeDetectingService" android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">
        <intent-filter>
            <action android:name="android.accessibilityservice.AccessibilityService"/>
        </intent-filter>
        <meta-data android:name="android.accessibilityservice" android:resource="@xml/accessibilityservice"/>
    </service>
    ....
</application>
```

res/xml/accessibilityservice.xml

```markup
<?xml version="1.0" encoding="utf-8"?>
<!-- These options MUST be specified here in order for the events to be received on first start in Android 4.1.1 -->
<accessibility-service xmlns:tools="http://schemas.android.com/tools"
                       android:accessibilityEventTypes="typeWindowStateChanged"
                       android:accessibilityFeedbackType="feedbackGeneric"
                       android:accessibilityFlags="flagIncludeNotImportantViews"
                       android:description="@string/accessibility_service_description"
                       xmlns:android="http://schemas.android.com/apk/res/android"
                       tools:ignore="UnusedAttribute"/>
```

### 3. ActivityManager

使用 ActivityManager有一定版本限制。官方文档有详细的说明：

```
on API-21 as of LOLLIPOP, ActivityManager.getRunningTasks() is no longer available to third partyapplications: the introduction of document-centric recents means it can leak person information to thecaller. For backwards compatibility, it will still return a small subset of its data: at least the caller's owntasks, and possibly some other tasks such as home that are known to not be sensitive.
```

网上有人说可以用下面的方法分别调用不同的API，兼容不同的版本:

```
Pre-Lollipop: ActivityManager.getRunningTasksLollipop: ActivityManager.getRunningAppProcesses
```

在 Android5.0 (API-21)及以上，getRunningTasks将只返回自己和 launcher，getRunningTasks 无法正确判断当前应用是否为front, 不同版本测试的结果也不一样：  
在 Android 5.0版本：  

1. 当用户在当前App页面时，runningTasksInfos.get(0) = 当前App  
2. 当用户回到launch页面时，runningTasksInfos.get(0) = launcher  
3. 当用户在其它App页面时，runningTasksInfos.get(0) = launcher  
   在 Android 5.1, 6.0 以上版本：  
4. 当用户在钱盾页面时，runningTasksInfos.get(0) = 当前App  
5. 当用户回到launch页面时，runningTasksInfos.get(0) = launcher  
6. 当用户在其它App页面时，runningTasksInfos.get(0) = 当前App  
   对于getRunningAppProcesses，经过我自己的测试：  
   在Android 5.0(API-21) 版本getRunningAppProcesses可以获取所有应用的process，可以通过其来判断top package; 但是在 Android 5.1.0 (API-22)及其以上Android 6.0 (API-23)上永远只返回应用自身的process，不能用来判断任意top package，但是可以用来判断当前App应用是否在前台：  
   官方文档申明 getRunningAppProcesses() 只是用于debugging and management user interfaces，有一定的局限性，说不定哪天就不支持了  
   依赖反射调用: ActivityManager.RunningAppProcessInfo.processState  
   无法监控 App switcher activity.

#### 代码示例：

```java
public class CurrentApplicationPackageRetriever {
    private final Context context;
    public CurrentApplicationPackageRetriever(Context context) {
        this.context = context;
    }
    public String[] get() {
        if (Build.VERSION.SDK_INT < 21)
            return getPreLollipop();
        else
            return getLollipop();
    }
    private String[] getPreLollipop() {
        @SuppressWarnings("deprecation")
        List<ActivityManager.RunningTaskInfo> tasks =
            activityManager().getRunningTasks(1);
        ActivityManager.RunningTaskInfo currentTask = tasks.get(0);
        ComponentName currentActivity = currentTask.topActivity;
        return new String[] { currentActivity.getPackageName() };
    }

    private String[] getLollipop() {
        final int PROCESS_STATE_TOP = 2;
        try {
            Field processStateField = ActivityManager.RunningAppProcessInfo.class.getDeclaredField("processState");

            List<ActivityManager.RunningAppProcessInfo> processes =
                activityManager().getRunningAppProcesses();
            for (ActivityManager.RunningAppProcessInfo process : processes) {
                if (
                    // Filters out most non-activity processes
                    process.importance <= ActivityManager.RunningAppProcessInfo.IMPORTANCE_FOREGROUND                    &&
                    // Filters out processes that are just being
                    // _used_ by the process with the activity
                    process.importanceReasonCode == 0
                ) {
                    int state = processStateField.getInt(process);

                    if (state == PROCESS_STATE_TOP)
                        /*                         If multiple candidate processes can get here,                         it's most likely that apps are being switched.                         The first one provided by the OS seems to be                         the one being switched to, so we stop here.                         */
                        return process.pkgList;
                }
            }
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
        return new String[] { };
    }

    private ActivityManager activityManager() {
        return (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
    }
}
```

添加 GET_TASKS 权限到 AndroidManifest.xml:

```markup
<!--suppress DeprecatedClassUsageInspection -->
<uses-permission android:name="android.permission.GET_TASKS" />
```

### 4.通过反射”android.app.ActivityThread”的方式

```java
public static Activity getRunningActivity() {
    try {
        Class activityThreadClass = Class.forName("android.app.ActivityThread");
        Object activityThread = activityThreadClass.getMethod("currentActivityThread")
                .invoke(null);
        Field activitiesField = activityThreadClass.getDeclaredField("mActivities");
        activitiesField.setAccessible(true);
        ArrayMap activities = (ArrayMap) activitiesField.get(activityThread);
        for (Object activityRecord : activities.values()) {
            Class activityRecordClass = activityRecord.getClass();
            Field pausedField = activityRecordClass.getDeclaredField("paused");
            pausedField.setAccessible(true);
            if (!pausedField.getBoolean(activityRecord)) {
                Field activityField = activityRecordClass.getDeclaredField("activity");
                activityField.setAccessible(true);
                return (Activity) activityField.get(activityRecord);
            }
        }
    } catch (Exception e) {
        throw new RuntimeException(e);
    }

    throw new RuntimeException("Didn't find the running activity");
}
```

- 在有些手机上测试时，发现 It’s API > 19,只能获取当前App的Activities
- 另外在当用“Map” 替换“ArrayMap”发现在4.3的手机可以工作正常. 更高版本的手机还没有测试过

### 5.通过 UsageStatsManager -> queryUsageStats方法

```java
if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
    UsageStatsManager usm = (UsageStatsManager) getSystemService("usagestats");
    long time = System.currentTimeMillis();
    List<UsageStats> appList = usm.queryUsageStats(UsageStatsManager.INTERVAL_DAILY, time - 1000 * 1000, time);
    if (appList != null && appList.size() > 0) {
        SortedMap<Long, UsageStats> mySortedMap = new TreeMap<Long, UsageStats>();
        for (UsageStats usageStats : appList) {
            mySortedMap.put(usageStats.getLastTimeUsed(), usageStats);
        }
        if (mySortedMap != null && !mySortedMap.isEmpty()) {
            currentApp = mySortedMap.get(mySortedMap.lastKey()).getPackageName();
        }
    }
} else {
    List<ActivityManager.RunningAppProcessInfo> tasks = am.getRunningAppProcesses();
    currentApp = tasks.get(0).processName;
}
```

不要忘记添加permission 到Manifest 文件：

```markup
<uses-permission android:name="android.permission.GET_TASKS" />

<uses-permission android:name="android.permission.PACKAGE_USAGE_STATS"
                 tools:ignore="ProtectedPermissions" />
```
