# JVM字节码执行引擎

> 执行引擎是JVM的核心组成部分之一。虚拟机是相对于物理机而言，这两种机器都具备执行代码的能力。
> - 物理机的执行引擎是直接建立在处理器、硬件、指令集和操作系统的层面上。
> - 虚拟机的执行引擎是由自己实现的。

- 执行引擎的具体实现：
  - 解释执行：通过解释器执行。
  - 编译执行：通过即时编译器产生本地代码执行。

- 执行引擎的表现形式：
  输入字节码文件---解析字节码文件---输出执行结果
```flow
st=>start: Start
e=>end
op1=>operation: 输入字节码文件
op2=>operation：解析字节码
op3=>operation：输出执行结果

st->op1->op2->op3->e
```
## 运行时栈帧结构
> 栈帧：支持JVM进行方法调用和方法执行的数据结构。它是JVM运行期间数据区中的虚拟机栈的栈元素。它存储了方法的局部变量表、操作数栈、动态链接、方法返回地址等信息。

- 每一个方法从调用开始到执行完成，就对应着一个栈帧在JVM里面从入栈到出栈的过程。
- 一个线程的方法调用链可能很长，很多方法同时处于执行状态。对于执行引擎来讲，在活动线程中，只有栈顶的栈帧是有效的，称为**当前栈帧**，这个栈帧所关联的方法称为**当前方法**。**执行引擎所运行的所有字节码指令都只针对当前栈帧进行操作。**


### 局部变量表
> 局部变量表：一组变量值存储空间，用于存放方法参数和方法内部定义的局部变量。

- 表容量：在编译期，在方法的Code属性的max_locals数据项中确定了该方法需要的最大容量。以**变量槽slot**为最小单位。JVM允许slot长度随着处理器、操作系统或者JVM的不同而变化。
- 对于64位数据类型的存储，JVM会以高位在前的方式为其分配两个连续的slot空间。64位的数据类只有long和double（long和double都是**非原子性协定**）。这里把long和double分开存储的做法与一次long和double数据类型读写分割为两次32位读写的做法类似。但由于局部变量表是线程私有的，因此是否是原子操作，都不会影响数据安全。
- 局部变量表使用**索引**定位：索引范围从0开始。如果是32位数据，索引n表示使用第n个slot，如果是64位数据，索引n表示使用n和n+1两个slot。
- 如果是**实例方法**，那么局部变量表的**第0位索引**存放的是方法所属实例对象的引用，在方法中使用this关键字可以访问这个参数。


### 操作数栈
> 操作数栈：后入先出，每一个栈元素可以是任意的Java数据类型。32位数据占用一个栈容量，64位数据占用两个栈容量。