# IJKPlayer优化记录 (二)关于reset方法的问题

#### 1. 切换播放地址只有声音，没有图像
**android mediaplayer状态机**
![image](http://upload-images.jianshu.io/upload_images/4858935-52700bee417826a1?imageMogr2/auto-orient/strip)

android mediaplayer切换播放地址可以release掉旧的mediaplayer，新建一个mediaplayer，也可以复用之前的mediaplayer实例，调用reset方法回到Idle状态，再重新setDataSource。

ijkplayer提供了与MediaPlayer相同的一套封装IjkMediaPlayer，**但调用reset方法同时会释放掉surface，**这点与MediaPlayer是不同的。因此按照之前的调用逻辑切换播放地址，可能会出现只有声音，画面却停留在上一个播放源的最后一帧。

解决办法是，如果还复用之前的SurfaceView，需要在reset之后调用IjkMediaPlayer的setDisplay方法，设置SurfaceHolder。
