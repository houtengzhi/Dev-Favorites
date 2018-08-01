# Android编程技巧

##### 1. 简化findViewById

在BaseActivity或BaseFragment中定义
```java
protected <T extends View> T $(View view, int resId) {
    return findViewById(view, resId);
}

private  <V extends View> V findViewById(Object object, int id) {
    if (object instanceof View) {
        return (V) ((View) object).findViewById(id);
    } else if (object instanceof Activity) {
        return (V) ((Activity) object).findViewById(id);
    } else if (object instanceof Window) {
        return (V) ((Window) object).findViewById(id);
    } else if (object instanceof Fragment) {
        return (V) ((Fragment) object).getView().findViewById(id);
    }
    return null;
}
```
初始化控件时
```java
btnGet = $(view, R.id.btn_get_regular_channels);
```

##### 2. 详解Android中Intent对象与Intent Filter过滤匹配过程
https://www.jb51.net/article/76528.htm