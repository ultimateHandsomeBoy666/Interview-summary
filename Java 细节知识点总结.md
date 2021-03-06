## Java 细节知识点总结

#### 1、Java 对象都是分配在堆上的吗？

new 出来的对象，一般都分配在堆上。但是在一些特殊情况下，Jvm 会对其进行优化。

如果一个对象在某个代码块中实例化，我们很容易认为它也应该在这个代码块中回收。如果该对象的生命周期超出了该代码块，就称其为**对象逃逸**。对象逃逸包括方法逃逸（方法将对象作为返回值）和线程逃逸（其他线程可以持有该对象）。

Jvm 可以对 Java 对象进行逃逸分析，如果认为对象在其生命周期不发生逃逸，那么会进行如下优化：

* 栈上分配对象

  方法入栈对象生成，方法出栈对象销毁

* 同步消除

  一个对象不会逃逸，那么他就只会被一个线程持有，就不要进行同步

* 标量替换

  将一个对象替换成等效的基本类型的组合

##### TLAB

JVM在内存在新生代Eden Space中开辟了一小块线程私有的区域，称作TLAB（Thread-local allocation buffer）。默认设定为占用Eden Space的1%。在Java程序中很多对象都是小对象且用过即丢，它们不存在线程共享也适合被快速GC，所以对于小对象通常JVM会优先分配在TLAB上，并且TLAB上的分配由于是线程私有所以没有锁开销。

因此在实践中分配多个小对象的效率通常比分配一个大对象的效率要高。

**参考：https://www.cnblogs.com/theRhyme/p/9528761.html**