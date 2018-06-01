# ARouter解析

## 2018=06-01
这里分析一下ARouter是如何将每个module下的由APT生成的path--Class对应的数据聚合到一起的？

猜想： apt生成Java源文件的时候，都在指定的package下。在运行时，通过扫描指定package下的所有Java文件，将其中的数据聚合起来。

首先看注解@Route生成的Java文件特点
``` java
public class ARouter$$Group$$app implements IRouteGroup {
  @Override
  public void loadInto(Map<String, RouteMeta> atlas) {
    atlas.put("/app/browser/activity", RouteMeta.build(RouteType.ACTIVITY, BrowserActivity.class, "/app/browser/activity", "app", null, -1, -2147483648));
  }
}
```
 - 都实现了接口IRouteGroup
 - 生成的Java源文件的报名为 com.alibaba.android.arouter.routes
 - 方法名为：loadInto, 而且参数为一个Map，并将数据添加到了该Map中

ok, 不足为据：
再看ARouter的初始化(如果要聚合就必须在初始化ARouter的时候将其聚合在一起，否则不同的module的Activity将不能跳转)
``` java
    public static void init(Application application) {
        //...
        hasInit = _ARouter.init(application);
        //...
    }

    protected static synchronized boolean init(Application application) {
        mContext = application;
        LogisticsCenter.init(mContext, executor);
        //...
    }

    public synchronized static void init(Context context, ThreadPoolExecutor tpe) throws HandlerException {
        //...
        try {
            long startInit = System.currentTimeMillis();
            Set<String> routerMap;

            // 在指定的package下寻找所有的class
            routerMap = ClassUtils.getFileNameByPackageName(mContext, ROUTE_ROOT_PAKCAGE);

            for (String className : routerMap) {
                if (className.startsWith(ROUTE_ROOT_PAKCAGE + DOT + SDK_NAME + SEPARATOR + SUFFIX_ROOT)) {
                    // 将数据添加到仓库
                    ((IRouteRoot) (Class.forName(className).getConstructor().newInstance())).loadInto(Warehouse.groupsIndex);
                //...
                }
            }
        }
    }
```

that's it.