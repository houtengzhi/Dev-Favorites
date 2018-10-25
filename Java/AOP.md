# AOP(面向切面编程)

## 1. AspectJ
* [关于AspectJ，你需要知道的一切](http://linbinghe.com/2017/65db25bc.html)

* [Android AOP编程的四种策略探讨：Aspectj，cglib+dexmaker，Javassist，epic+dexposed](https://windysha.github.io/2018/01/18/Android-AOP编程的四种策略探讨：Aspectj，cglib-dexmaker，Javassist，epic-dexposed/)

* [Android AOP面向切面编程和Gradle插件编写(一)](https://www.jianshu.com/p/6d8054759bca)

* [Spring-AOP @AspectJ进阶之增强织入的顺序](https://blog.csdn.net/yangshangwei/article/details/77932177)

## 2. AspectJ中的优先级
有时候，多个通知（Advice）可能会作用于同一个连接点（JoinPoint）。这种情况下，通知Advice解析的顺序基于切面Aspect的优先级。

#### 1 通知Advice的优先级规则

##### 1.1 Advice在不同的Aspect中定义时
当两个advice定义在不同的切面aspect时，有三条情况：

在一定的优先级声明形式下，如果Aspect A匹配早于Aspect B时，那么对于相同的连接点，Aspect A的所有Advice的优先级都高于Aspect B。
否则，如果Aspect A是Aspect B的子Aspect，那么Aspect A的Advice优先级高于Aspect B的。因此，除非指定了优先级声明，子Aspect的Advice的优先级都高于父Aspect的。
否则，如果两个Advice定义在不同的Aspect中，他们的优先级则无法确定。

##### 1.2 Advice在相同的Aspect中定义时
如果两个Advice定义在同一个Aspect ，有两种情况：

如果他们都是after advice，那么Aspect 中后定义的advice优先级高于先定义的advice。
否则，Aspect 中先定义的advice优先级高于后定义的advice。

#### 2 优先级的声明
```java
public aspect SystemArchitecture {
       declare precedence : Security*, TransactionSupport, Persistence;

       // ...
     }

//can be written as:
@Aspect
@DeclarePrecedence("Security*,org.xyz.TransactionSupport,org.xyz.Persistence")
public class SystemArchitecture {

       // ...
     }
```



## 埋点技术
* [Android埋点技术分析](http://unclechen.github.io/2017/12/18/Android埋点技术分析/)