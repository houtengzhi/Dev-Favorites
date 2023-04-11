# RxJava学习笔记(一)

##### 1. 用 RxJava 封装回调方法 CallBack
https://glumes.com/post/android/rxjava-wrapper-callback/

##### 2. RxJava单元测试
异步变同步
参考 http://www.jianshu.com/p/cdfeb6c3d099#
注意ComputationScheduler也需切换到当前线程，否则timer、delay操作无法测试。
RxJavaPlugins.getInstance().reset()方法在低版本RxJava中是private。

```Java
public static void openRxTools() {
        if (isInitRxTools) {
            return;
        }
        isInitRxTools = true;

        RxAndroidSchedulersHook rxAndroidSchedulersHook = new RxAndroidSchedulersHook() {
            @Override
            public Scheduler getMainThreadScheduler() {
                return Schedulers.immediate();
            }
        };

        RxJavaSchedulersHook rxJavaSchedulersHook = new RxJavaSchedulersHook() {
            @Override
            public Scheduler getIOScheduler() {
                return Schedulers.immediate();
            }

            //定时器线程也需要切换到当前线程
            @Override
            public Scheduler getComputationScheduler() {
                return Schedulers.immediate();
            }
        };

        // reset()不是必要，实践中发现不写reset()，偶尔会出错，所以写上保险^_^
        RxAndroidPlugins.getInstance().reset();
        RxAndroidPlugins.getInstance().registerSchedulersHook(rxAndroidSchedulersHook);
        RxJavaPlugins.getInstance().reset();
        RxJavaPlugins.getInstance().registerSchedulersHook(rxJavaSchedulersHook);
    }
```