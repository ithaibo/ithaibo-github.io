# Android权限申请 #
在Android 6.0（API < 23）以前，App用到的权限只需要在AndroidManifest文件中声明就好了。在6.0以后，权限策略进行了更改，权限分为一般权限和危险权限。一般权限跟以前一样不用动态申请，应用安装完毕就已经授予；危险权限需要在使用的使用动态申请。


## 一般权限 ##

> [If an app declares in its manifest that it needs a normal permission, the system automatically grants the app that permission at install time](https://developer.android.com/guide/topics/permissions/normal-permissions.html).

下面是一般权限列表：

 - ACCESS_LOCATION_EXTRA_COMMANDS
 - ACCESS_NETWORK_STATE
 - ACCESS_NOTIFICATION_POLICY
 - ACCESS_WIFI_STATE
 - BLUETOOTH
 - BLUETOOTH_ADMIN
 - BROADCAST_STICKY
 - CHANGE_NETWORK_STATE
 - CHANGE_WIFI_MULTICAST_STATE
 - CHANGE_WIFI_STATE
 - DISABLE_KEYGUARD
 - EXPAND_STATUS_BAR
 - GET_PACKAGE_SIZE
 - INSTALL_SHORTCUT
 - INTERNET
 - KILL_BACKGROUND_PROCESSES
 - MODIFY_AUDIO_SETTINGS
 - NFC
 - READ_SYNC_SETTINGS
 - READ_SYNC_STATS
 - RECEIVE_BOOT_COMPLETED
 - REORDER_TASKS
 - REQUEST_IGNORE_BATTERY_OPTIMIZATIONS
 - REQUEST_INSTALL_PACKAGES
 - SET_ALARM
 - SET_TIME_ZONE
 - SET_WALLPAPER
 - SET_WALLPAPER_HINTS
 - TRANSMIT_IR
 - UNINSTALL_SHORTCUT
 - USE_FINGERPRINT
 - VIBRATE
 - WAKE_LOCK
 - WRITE_SYNC_SETTINGS

## 危险权限 ##
像读取联系人这样，企图获取涉及用户隐私的数据或资源、潜在影响用户存储的数据及操作其他应用的行为都触及危险权限。

如果声明了危险权限，必须由用户授予相应的权限。

危险权限一般都是一组权限，如果该组中的一个权限被授予，同组其他的权限都将被自动授予。

[危险权限列表](https://developer.android.com/guide/topics/permissions/requesting.html#normal-dangerous)

## 动态权限申请 ##
1. 检查权限是否已经被授权
2. 申请权限
3. 如果已经授权成功，执行相关操作

以下是相关代码：

``` Java
        // here, check the permission is granted or not
        if (ContextCompat.checkSelfPermission(mContext,
                Manifest.permission.CALL_PHONE)!=
                PackageManager.PERMISSION_GRANTED
        ) {
            // request permission
            ActivityCompat.requestPermissions(this,
                    new String[]{Manifest.permission.CALL_PHONE},
                    MY_PERMISSIONS_REQUEST_CALL_PHONE);
        } else {
            // do something
            callPhone("10086");
        }
```

