---
layout: post
title:  Java类加载器的一些事
description: Class Loaders In Java
modified: 2018-06-05
tags: tech software-engineering java
---

### 什么是类加载器
类加载器负责在运行时动态地将Java类加载进JVM(Java虚拟机)，同时，它们也是JRE(Java运行时环境)的一部分。因此，借助类加载器JVM无需知道底层文件或文件系统来运行Java程序。此外，Java类并不是一次全部被加载进内存，而是在应用程序需要时加载。这就是类加载器作用之处，它们负责将文件系统、网络或其他来源的类文件加载进内存。  
在本文，我们将讨论不同类型的内置类加载器，它们如何工作以及关于自定义内加载器的实现。

### 内置的类加载器
让我们从一个简单的例子开始，学习不同的类如何被类加载器加载：
```java
public void printClassLoaders() throws ClassNotFoundException {
  System.out.println("Classloader of this class: "
    + PrintClassLoader.class.getClassLoader());

  System.out.println("Classloader of Logging: "
    + Logging.class.getClassLoader());

  System.out.println("Classloader of ArrayList: "
    + ArrayList.class.getClassLoader());
}
```
这是上述代码被执行后的输出：
```
Classloader of this class: sun.misc.Launcher$AppClassLoader@18b4aac2
Classloader of Logging: sun.misc.Launcher$ExtClassLoader@1d44bcfa
Classloader of ArrayList: null
```
如上，这里有三种不同的类加载器：Application，Extension以及Bootstrap类加载器(显示为null，为何呢？)。

+ Application类加载器加载了包含示例方法的类，它用于加载在类路径中的应用自己的文件
+ Extension类加载器加载了Logging类，它用于加载标准核心Java类的扩展类
+ Bootstrap类加载器加载了ArrayList类，它是所有其他加载器的父类

但是，我们可以看到最后一个输出，对于ArrayList是null，这是因为Bootstrap类加载器是用本地代码编写的，而不是Java，因此它不会显示为Java类。由于这个原因，Bootstrap类加载器的行为在不同的JVM之间会有所不同。

---

现在，让我们来更详细地聊一聊各个类加载器。
+ __Bootstrap类加载器__

  Java类由 _java.lang.ClassLoader_ 的实例加载。但是，类加载器本身也是类，因此，问题来了，谁负责加载 _java.lang.ClassLoader_？这就是Bootstrap类加载器起作用的地方。它主要用于负责加载JDK相关的类，通常是`rt.jar`以及位于`$JAVA_HOME/jre/lib`目录下的其他核心库。此外，Bootstrap类加载器是其他所有类加载器实例的父类。  
  Bootstrap类加载器是JVM核心的一部分，并且使用本地代码编写，正如上述示例所示。不同的平台可能对这个特定的类加载器有不同的实现。

+ __Extension类加载器__

  Extension类加载器是Bootstrap类加载器的子类，用于负责加载标准核心Java类的扩展，所以它对于所有运行在该平台上的应用都可用。Extension类加载器从JDK的扩展目录下加载，通常是`$JAVA_HOME/lib/ext`目录或者是系统属性`java.ext.dirs`中配置的任何其他目录。

+ __Application类加载器__

  Application类加载器会负责将所有应用程序级别的类加载到JVM中。它加载在 _classpath_ 环境变量中找到的文件。此外，它是Extension类加载器的子类。

---

### 类加载器如何工作
类加载器是Java运行时环境即JRE的一部分，当JVM请求一个类时，类加载器会尝试根据完全限定类名(package.ClassName)来定位该类并将类定义加载进运行
时。    
_java.lang.ClassLoader.loadClass()_ 方法负责将类定义加载进运行时，该方法会依据类的完全限定名来加载。如果该类尚未被加载，则该请求会被委托给父类加载器，这个过程是递归进行的。最终，如果父类加载器均未能找到并加载该类，那么子类加载器将调用 _java.net.URLClassLoader.findClass()_ 方法在文件系统中查找该类。如果最后一个子类加载器也未能加载该类，则会抛出 _java.lang.ClassNotFoundException_ 异常。

以下是 _ClassNotFoundException_ 异常的示例输出：
```
java.lang.ClassNotFoundException: TestClassLoaderClass
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:338)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:264)
	at com.learning.zxiao.Main.tryToLoadClass(Main.java:31)
	at com.learning.zxiao.Main.main(Main.java:12)
```
如果查看 _java.lang.Class.forName()_ 的调用执行序列，我们可以明白它首先尝试通过父类加载器去加载所需的类，然后再通过 _java.net.URLClassLoader.findClass()_ 来查找该类。若仍无法找到该类，就会抛出 _ClassNotFoundException_ 异常。

![Java ClassLoader](/assets/images/java-class-loader.png)

依据类加载器的工作机制，类加载器有如下三个重要特性：  
__委托模式__

类加载器遵循委托模式，在响应请求来查找类或资源时，ClassLoader的实例会将类或资源的搜索委托给其父类加载类。
假设我们有一个将应用程序类加载到JVM中的请求，Application类加载器首先将该类的加载委派给Extension类加载器，然后将其委托给Bootstrap类加载器。只有在Bootstrap和Extension类加载器在加载类不成功时，Application类加载器才会尝试加载该类。换言之，父类加载器加载过的类不能被子类加载器再次加载，每个类仅会被加载一次。

__单一性__

委派模式带来是我们能容易地确保所加载类的独特唯一，因为我们总是通过向上委派来搜索加载类。类加载器的任务是根据一个类的全限定名来读取此类的二进制字节流到JVM中，然后转换为一个与目标类对应的java.lang.Class对象实例。在JVM中表示两个class对象是否为同一个类对象存在两个必要条件：
1. 类的完整类名必须一致，包括包名
2. 加载这个类的ClassLoader(指ClassLoader实例对象)必须相同

__可见性__

子类加载器可以看见所有由父类加载器加载的类，反之则不然。

---

### 自定义类加载器
```java
public class DemoClassLoader extends ClassLoader {
    public DemoClassLoader(ClassLoader parent) {
        super(parent);
    }

    public Class getClass(String name) throws ClassNotFoundException {
        byte[] b = loadClassFromRepo(name);
        return defineClass(name, b, 0, b.length);
    }

    @Override
    public Class<?> loadClass(String name) throws ClassNotFoundException {
        if (name.startsWith("com.learning.zxiao")) {
            System.out.println("Loading Class from Demo Class Loader");
            return getClass(name);
        }

        return super.loadClass(name);
    }

    private byte[] loadClassFromRepo(String fileName)  {
        // Returns a byte array from specified file.
    }
}
```

现在来看看自定义类加载器所继承的抽象类 _java.lang.ClassLoader_ 中几个重要方法：
+ __loadClass()方法__

  看一下JDK源码：
  ```java
  protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // First, check if the class has already been loaded
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
  ```
  该方法是类加载器的入口，负责根据给定的完全限定类名参数来加载类。JVM通过调用该方法并将 _resolve_ 参数设置为 _true_ 来解析类引用，但并不总是需要去解析类，若只是想确定类是否存在，则 _resolve_ 参数设置为 _false_ 即可。通过源码可知，加载类的默认顺序是：
  1. 调用 _findLoadedClass(String)_ 方法来查看该类是否已经加载
  2. 在父类加载器上调用 _loadClass(String)_ 方法
  3. 调用 _findClass(String)_ 方法来查找类

+ __defineClass()方法__

  方法签名：`protected final Class<?> defineClass(String name, byte[] b, int off, int len) throws ClassFormatError`，该方法负责将字节数组转换成Class的实例，在使用该类之前，我们需要解析它。如果数据不包含有效的类，则会抛出 _ClassFormatError_。并且，该方法被标记为 _final_，因此无法被重写。

+ __findClass()方法__

  该方法以完全限定类名作为参数来查找类，需要在遵循委托模式的自定义类加载器中重写此方法。另外，如果父级类加载器找不到所请求的类，则 _loadClass()_ 方法将调动此方法。若类加载器的父级都没有找到该类，则会抛出默认实现的 _ClassNotFoundException_。

+ __getParent()方法__

  方法签名：`public final ClassLoader getParent()`，该方法用于返回委托模式下的父级类加载器。

+ __getResource()和findResource()方法__

  方法签名：`public URL getResource(String name)`，此方法尝试查找指定名称的资源，也同样遵循委托模式的工作机制。_findResource()方法_ 会返回一个用于读取资源的URL对象，如果资源找不到或者调用者没有足够的权限返回资源，则返回 _null_。需要注意的是Java从类路径中寻找并加载资源，上述方法的参数对于类路径可以是相对或绝对的。

---

### Context类加载器
通常，context类加载器为J2SE中引入的类加载委派方案提供了一种替代方法。正如上文所述，JVM中的类加载器遵循层次模型，每个类加载器都有一个父类，但引导类加载器除外。但是，有时当JVM核心类需要动态加载由应用程序开发人员提供的类或资源时，我们可能会遇到问题。  
例如，在JNDI中，核心功能是由rt.jar中的Bootstrap类来实现的，但是这些JNDI类可能会加载部署在应用程序类路径中的且由独立供应商实现的JNDI提供者。这个场景需要Bootstrap类加载器（父类加载器）加载之于Application加载器（子类加载器）可见的类。J2SE委托模式在这里不起作用（原因见上述特性三），为了解决这个问题，我们需要找到类加载的替代方法 -- 可通过线程上下文装载器(thread context loader)来实现。  
_java.lang.Thread_ 类有一个方法 _getContextClassLoader()_，它返回特定线程的 _ContextClassLoader_，该 _ContextClassLoader_ 由此线程的创建者在加载资源和类时提供。

---

### 小结
本文于开始处，首先说明了类加载器对于执行Java程序的重要性。然后分别介绍了三种重要的内置类加载器，即Bootstrap，Extension和Application类加载器：Bootstrap充当所有这些类加载器的父代，负责加载JDK内部类；另一方面，Extension和Application类加载器分别从Java扩展目录和应用程序的类路径下加载类。接着，我们分析了类加载器的工作机制及其带来的重要特性代理，然后简要说明如何创建自定义加载器。在最后，介绍了Context类加载器及其应用的典型场景。

---

> 学习整理，不足差错之处，敬请指正。转载请注明出处
