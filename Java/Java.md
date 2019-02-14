## Java基础

#### 泛型

* [【启舰博客】 夯实JAVA基本之——泛型详解(1)：基本使用](http://blog.csdn.net/harvic880925/article/details/49872903)
* [【启舰博客】 夯实JAVA基本之——泛型详解(2)：高级进阶](http://blog.csdn.net/harvic880925/article/details/49883589)


#### 反射

* [Java反射以及在Android中的特殊应用](https://juejin.im/post/5a2c1c5bf265da431956334c)


#### 代理
* [Java代理以及在Android中的简单应用](https://juejin.im/post/5a2e4e9a51882559e2259ad3)

* [Java动态代理机制详解（JDK 和CGLIB，Javassist，ASM）](https://blog.csdn.net/luanlouis/article/details/24589193)


#### 注解
* [深入理解Java注解类型(@Annotation)](https://blog.csdn.net/javazejian/article/details/71860633)
* [【Trinea】公共技术点之 Java 注解 Annotation](http://a.codekk.com/detail/Android/Trinea/公共技术点之%20Java%20注解%20Annotation)
* [Android 编译时注解 —— 语法详解](https://blog.csdn.net/gdutxiaoxu/article/details/70822023)
* [【鸿洋博客】Android 如何编写基于编译时注解的项目](https://blog.csdn.net/lmj623565791/article/details/51931859)
* [Android注解-编译时生成代码 (APT)](https://blog.csdn.net/a1018875550/article/details/52166916)

#### 并发
* ["聊胜于无",浅析Java中的原子操作](https://cloud.tencent.com/developer/article/1124658)

## Issues

#### 1. java annotation 中 SOURCE 和 CLASS 的区别？
作者：RednaxelaFX
链接：https://www.zhihu.com/question/60835139/answer/180750670
来源：知乎

* RetentionPolicy.SOURCE 和 CLASS 是用于不同场景的。RetentionPolicy.SOURCE：只在本编译单元的编译过程中保留，并不写入Class文件中。这种注解主要用于在本编译单元（这里一个Java源码文件可以看作一个编译单元）内触发注解处理器（annotation processor）的相关处理，例如说可以让注解处理器相应地生成一些代码，或者是让注解处理器做一些额外的类型检查，等等。
* RetentionPolicy.CLASS：在编译的过程中保留并且会写入Class文件中，但是JVM在加载类的时候不需要将其加载为运行时可见的（反射可见）的注解。这里很重要的一点是编译多个Java文件时的情况：假如要编译A.java源码文件和B.class文件，其中A类依赖B类，并且B类上有些注解希望让A.java编译时能看到，那么B.class里就必须要持有这些注解信息才行。同时我们可能不需要让它在运行时对反射可见（例如说为了减少运行时元数据的大小之类），所以会选择CLASS而不是RUNTIME。
* RetentionPolicy.RUNTIME：在编译过程中保留，会写入Class文件，并且JVM加载类的时候也会将其加载为反射可见的注解。这就不用多说了，例如说Spring的依赖注入就会在运行时通过扫描类上的注解来决定注入啥。

#### 2. 动态代理、Hook、AOP、插件化技术的联系与区别
链接：https://www.jianshu.com/p/480b2eddea9c

动态代理三种实现方法：

1. JDK的动态代理：就是在程序运行的过程中，根据被代理的接口来动态生成代理类的class文 件，并加载运行的过程。
2. cglib动态代理：cglib封装了asm,可以在运行期动态生成新的class，以此来实现动态代理。
3. Javassist：Javassist是一个开源的分析、编辑和创建Java字节码的类库。

AOP有两类实现方式：

 1. 运行时AOP:运行时动态生成子类字节码(Cglib)或代理类字节码(JDK动态代理)或直接修改字节码(Javassist、ASM、AspectJ)
 2. 编译时AOP:编译时，修改字节码(ASM、AspectJ)或生成代理类(APT)



