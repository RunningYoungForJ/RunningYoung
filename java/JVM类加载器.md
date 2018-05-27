# JVM类加载器

> 类加载过程中的加载阶段在JVM的外部实现。这样做可以让应用程序自己决定如何去获取所需要的类。实现这个动作的代码模块就是**类加载器**。



## 类与类加载器的关系
> 对于任意一个类，都需要由加载它的类加载器和这个类本身一同确定该类在JVM中的唯一性。即比较两个类是否相等，只有在这两个类是由同一个类加载器加载的前提下才有意义，否则，即使这两个类来源于同一个Class文件，只要加载它们的类加载器不同，那么这两个类必定不相等。
> - 这里的“相等”：包括代表类的Class对象的equals方法、isAssignableFrom方法和isInstance方法的返回结果，也包括了使用instanceof关键字做对象所属关系判定等情况。

```java
package ThinkingInJVM;

import java.io.IOException;
import java.io.InputStream;
public class ClassLoadTest {
    public static void main(String args[])throws Exception{
        ClassLoader myLoad=new ClassLoader() {
            @Override
            protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
                try {
                    String fileName=name.substring(name.lastIndexOf(".")+1)+".class";
                    InputStream is=getClass().getResourceAsStream(fileName);
                    if (is==null){
                        return super.loadClass(name);
                    }
                    byte[] b=new byte[is.available()];
                    is.read(b);
                    return defineClass(name,b,0,b.length);
                }
                catch (IOException e){
                    throw new ClassNotFoundException();
                }
            }
        };

        Object obj=myLoad.loadClass("ThinkingInJVM.ClassLoadTest").newInstance();

        System.out.println(obj.getClass());
        System.out.println(obj instanceof ThinkingInJVM.ClassLoadTest);
    }
}

```
在上述代码中，创建了myLoad对象，并重写了类加载器的loadClass方法，构造了一个自定义的类加载器。在最后的比较中，虽然obj.getClass（）返回了ThinkingInJVM.ClassLoadTest，但在instanceof的比较中，结果是false。
- 这是因为JVM中存在两个ThinkingInJVM.ClassLoadTest类，一个是由系统类加载器加载的，另一个是由自定义的myLoad类加载器加载的。虽然来自同一个Class文件，但依旧是两个独立的类。


## 双亲委派模型
> 站在JVM的角度，只存在两种不同的类加载器：
> > 一种是启动类加载器（Bootstrap ClassLoader），是由C++实现，是JVM的一部分。
> > 另外一种是所有其他的类加载器，由Java实现，独立于JVM，并且都继承自java.lang.ClassLoader.
>
> 站在开发人员的角度，可以分为三类：
> > 启动类加载器
> > 扩展类加载器
> > 应用程序类加载器
> > ![Alt text](http://img.blog.csdn.net/20160102154038185)
> > 双亲委派模型：除了顶层的启动类加载器外，其余的类加载器都有自己的父类加载器。这里的父子关系不是通过继承实现，而是采用组合的方式复用父加载器的代码。


### 双亲委派模型工作过程
1. 如果一个类加载器收到了类加载请求。
2. 它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器完成。
3. 每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中。
4. 只有当父加载器无法完成这个加载请求（它的搜索范围内没有找到所需的类）时，子加载器才会尝试自己去加载。


### 双亲委派模型的特点

1. Java类随着它的类加载器一起具备了一种带有优先级的层次关系。
2. 如果没有使用双亲委派模型，由各个类加载器自行去加载的话，有可能会出现由多个加载器加载的不同的同名类。

> 双亲委派模型实现代码集中在java.lang.ClassLoader的loadClass（）方法中。

```java
protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
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
loadClass（）方法执行逻辑：
1. 首先查找是否已经被加载。
2. 若没有被加载过，则调用父加载器的loadClass（）方法。
3. 若父加载器为空，则默认使用启动类加载器作为父加载器进行加载。
4. 如果上述加载均失败，则抛出ClassNotFoundException异常，再调用自己的findClass（）方法进行加载。






