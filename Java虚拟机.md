Java 虚拟机



JDK ： java-language | jvm | java API    (Java Develope Kit java 最小开发单元环境)

JRE ：JVM | API | Java-SE-API （Java Runtime Enviornment java 运行的标准环境工具集）



Java 内存管理

------

JVM 运行时数据区域

###### 线程共享区域

- Method Area :  方法区用来存储常量，静态变量，以及类编译时产生的一些代码数据，这个区域又被称为“永久代”，因为此区域内存回收机制的策略比较宽松，主要针对常量池的回收和类型的卸载，甚至可以选择不实现内存回收机制，此区域规定如果不满足内存分配需求时，将抛出 OOM 异常；
- Runtime constant pool: 运行时常量池，属于 Method Area 的一部分，/////**To Be Continued！**
- Heap: Java 堆，是虚拟机所管理的区域里内存最大的一块区域，这里存放了虚拟机启动后的所有的对象（此说法不是绝对的），也是 Java 内存回收机制重点管理的对象，因此 Heap 也被称作为 GC 堆；Java 堆的分配只要求逻辑连续，对物理空间的连续是不做要求的，如果堆空间已经无法扩展，并且此时堆中没有内存分配给剩余的对象实例化，那么会抛出 OOM 错误；此处只阐述下概念，后续再详细学习；

###### 线程独享区域

- VM stack: 虚拟机栈就是我们常说的堆栈中的栈，它的生命周期和线程相同，用来存储常量以及引用对象，java 方法运行的时候，就是每个栈帧在栈中的入栈和出栈，虚拟机栈的内存分配在编译器完成，其中要注意的是 long double 这两个类型的变量占用两个 slot 空间，其它占一个；方法在运行的时候，虚拟机栈的内存大小是完全确定的，方法的运行不会改变它的大小；该区域会抛出 StackOverFlowError 和 OutOfMemoryError ;
- Native Method Stack:  和虚拟机栈作用类似，为 Native 方法服务，其中也定义了 StackOverFlowError 和 OutOfMemoryError；
- Program Counter Register：程序执行的字节码的行号指示器，字节码解释器就是通过改变这个计数器的值来选取下一条需要执行的字节码指令；多线程切换，线程恢复到正确的执行位置也是靠这个指示器；各个线程的指示器都是独立互不影响的，存储在线程的私有内存里；如果是 java 方法，该值未字节码的内存地址，如果是 Native 方法，该值未 Undefined，该内存空间是唯一一个未定义 OOM 的区域；
- 