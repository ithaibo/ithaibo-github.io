# Annotation 解析
## 运行时Annotation解析
运行时注解解析的主要API：
``` Java
//获取该方法上的指定注解
public <A extends Annotation> A getAnnotation(Class<T> annotationClass);

//获取该方法上的所有注解
public Annotation[] getAnnotations();

//检查该注解是否在方法上
public Annotation[] isAnnotationPresent(Class<? extends Annotation> annotationClass);
```
## 编译时Annotation解析
处理内容为：
 - 自定义类继承与AbstractProcessor
 - 重写其中的process方法
