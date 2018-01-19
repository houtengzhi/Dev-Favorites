## 博客
* [[老罗的Android之旅] Android控件TextView的实现原理](http://blog.csdn.net/luoshengyang/article/details/8636153)

* [EditText密码框小圆点替换成其它符号](http://blog.csdn.net/jdsjlzx/article/details/25064867)


## 笔记
* EditText的输入类型为密码时，字符隐藏为小圆点的时间可以通过修改 PasswordTransformationMethod类中的内部类Visible
```java
private static class Visible
    extends Handler
    implements UpdateLayout, Runnable
    {
        public Visible(Spannable sp, PasswordTransformationMethod ptm) {
            mText = sp;
            mTransformer = ptm;
			//延迟时间，默认为1500ms
            postAtTime(this, SystemClock.uptimeMillis() + 1500);
        }

        public void run() {
            mText.removeSpan(this);
        }

        private Spannable mText;
        private PasswordTransformationMethod mTransformer;
    }
```


