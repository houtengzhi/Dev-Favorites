# Android源码: View.post()方法解析

本文源码是4.4.2

`View.java`文件中post方法
```Java
public boolean post(Runnable action) {
        final AttachInfo attachInfo = mAttachInfo;
        if (attachInfo != null) {
            return attachInfo.mHandler.post(action);
        }
        // Assume that post will succeed later
        ViewRootImpl.getRunQueue().post(action);
        return true;
    }
```
如果`attachInfo`不为null会执行`mHandler.post(action)`，否则执行`getRunQueue().post(action)`。

`getRunQueue()`返回一个RunQueue对象，注意此处RunQueue定义为ThreadLocal类型，每个线程所使用的RunQueue是不同的副本。
```java
static RunQueue getRunQueue() {
        RunQueue rq = sRunQueues.get();
        if (rq != null) {
            return rq;
        }
        rq = new RunQueue();
        sRunQueues.set(rq);
        return rq;
    }
```
最后会执行到RunQueue 中的postDelayed()方法，将Runnable对象插入到一个ArrayList中。

```java
static final class RunQueue {
        private final ArrayList<HandlerAction> mActions = new ArrayList<HandlerAction>();

        void post(Runnable action) {
            postDelayed(action, 0);
        }

        void postDelayed(Runnable action, long delayMillis) {
            HandlerAction handlerAction = new HandlerAction();
            handlerAction.action = action;
            handlerAction.delay = delayMillis;

            synchronized (mActions) {
                mActions.add(handlerAction);
            }
        }

        void removeCallbacks(Runnable action) {
            ....
        }

        void executeActions(Handler handler) {
            synchronized (mActions) {
                final ArrayList<HandlerAction> actions = mActions;
                final int count = actions.size();

                for (int i = 0; i < count; i++) {
                    final HandlerAction handlerAction = actions.get(i);
                    handler.postDelayed(handlerAction.action, handlerAction.delay);
                }

                actions.clear();
            }
        }
```
那么RunQueue中暂存的Runnable什么时候会得到执行呢？追踪到`performTraversals()`方法中调用了`RunQueue.executeActions(Handler handler)`方法。
```java
private void performTraversals() {
...
// Execute enqueued actions on every traversal in case a detached view enqueued an action
 getRunQueue().executeActions(attachInfo.mHandler);
...
}
```
`performTraversals()`方法是在UI线程调用，因此getRunQueue()会返回UI线程的RunQueue，最终也是调用`handler.postDelayed()`方法把暂存的Runnable post到messagequeue中去等待执行，这里传入的Handler就是主线的Handler。由于`performTraversals()`方法也是被包装成Runnable post到主线程looper的`MessageQueue`中，因此会等到`performTraversals()`执行完再调用后面的Runnable。

#### 总结
**1.** 在子线程中调用`view.post()`时，如果view没有被attach，此时`attachInfo`为空，则会将要执行的Runnable插入到该子线程对应的RunQueue副本中，致使在调用`performTraversals()`时永远不会被执行到，还可能导致内存泄漏问题。

#### 参考文章
[通过View.post()获取View的宽高引发的两个问题](https://blog.csdn.net/scnuxisan225/article/details/49815269)