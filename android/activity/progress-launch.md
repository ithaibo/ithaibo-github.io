# Activity的启动过程
1. startActivityForResult
2. Instrumentation.execStartActivity
3. ActivityNatibeManager.getDefault().startActivity() (通过Binder调用AMS)
4. ActivityManagerService.startActivity
5. ActivityManagerService.startActivityAdsUser
6. ActivityStackSupervisor.startActivityMayWait
7. ActivityStack.resymeTopActivitiesLocked
8. ActivityStackSupervisor.startSpecificActivityLocked
9. com.android.server.am.ActivityStackSupervisor#realStartActivityLocked
10. IApplicationThread.scheduleLaunchActivity
11. ApplicationThread.scheduleLaunchActivity
12. android.app.ActivityThread#handleLaunchActivity
13. android.app.ActivityThread#performLaunchActivity
    - 获取Activity的组件信息
    - android.app.Instrumentation#newActivity(java.lang.ClassLoader, java.lang.String, android.content.Intent)（通过对应的ClassLoader创建对象）
    - android.app.LoadedApk#makeApplication
      - android.app.Instrumentation#callApplicationOnCreate
    - activity.attach
    - android.app.Instrumentation#callActivityOnCreate(android.app.Activity, android.os.Bundle, android.os.PersistableBundle)
