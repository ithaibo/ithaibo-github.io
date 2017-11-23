## Android Debug版本判断
在组件化开发中，如何在每个module中判断是否为Debug模式？

为了尽可能的减少对其他模块的依赖，可以ApplicationInfo.FLAG_DEBUGGABLE
来判断是否为Debug模式。

```Java
    private static Boolean isDebug = null;

    public static boolean isDebug() {
        return isDebug == null ? false : isDebug.booleanValue();
    }
 
    /**
     * Sync lib debug with app's debug value. Should be called in module Application
     *
     * @param context
     */
    public static void syncIsDebug(Context context) {
        if (isDebug == null) {
            isDebug = context.getApplicationInfo() != null &&
                    (context.getApplicationInfo().flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0;
        }
    }
```