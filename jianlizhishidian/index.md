# 简历知识点


1.埋点 有哪些方式 原理

1.AOP:面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术

AspectJ
在编译时候,动态对 class 文件进行修改

主要实现方式是动态代理
InvocationHandler

2.lifecycle
原理,利用 fragment 感知 activity 生命周期
fragment 拿到 LifecycleObserver 对象,然后再不同的生命周期回调

2.Tinker 热更新原理

```
1.保留第一次打包的 APK 以及 mapping 文件
2.第二次打包时候,跟原 apk 进行 diff
3.将变更的代码做成apk包
4.下发的时候将补丁包内的dex放到dex队列最前面
由于Tinker的方案是基于Multidex实现的修改dexElements的顺序实现的，所以最终还是要修改classLoder中dexPathList中dexElements的顺序。Android中有两种ClassLoader用于加载dex文件，BootClassLoader、PathClassLoader和DexClassLoader都是继承自BaseDexClassLoader

```

3 Retrifit2

```
涉及到的设计模式
外观模式，构建者模式，工厂模式，代理模式，适配器模式，策略模式，观察者模式

App应用程序通过Retrofit请求网络，实际上是使用Retrofit接口层封装请求参数，之后由OkHttp完成后续的请求操作。
在服务端返回数据之后，OkHttp将原始的结果交给Retrofit，Retrofit根据用户的需求对结果进行解析。
完成数据的转化(converterFactory)，适配(callAdapterFactory)，通过设计模式进行各种扩展。

使用Retrofit的七步骤
添加Retrofit依赖，网络权限
定义接收服务器返回数据的Bean
创建网络请求的接口，使用注解(动态代理，核心)
builder模式创建Retrofit实例，converter，calladapter...
创建接口实例，调用具体的网络请求
call同步/异步网络请求
处理服务器返回的数据

Retrofit网络通信八步骤
创建Retrofit实例
定义网络请求接口，并为接口中的方法添加注解
通过动态代理生成网络请求对象
通过网络请求适配器将网络请求对象进行平台适配
通过网络请求执行器，发送网络请求(call)
通过数据解析器解析数据
通过回调执行器，切换线程
用户在主线程处理返回结果




```

