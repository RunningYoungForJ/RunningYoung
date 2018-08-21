# Git底层存储原理

1. 从远端克隆或本地新建一个文件夹，使用

   ```shell
   git init
   ```

   会在本地创建一个Git仓库，一个初始化的Git仓库，主要包含的文件及文件夹有：

   ![初始化Git仓库](/Users/yangkun/Desktop/屏幕快照 2018-06-08 下午7.09.29.png)

2. 主要的配置及数据存储位于.git文件夹下：

   ![.git文件夹](/Users/yangkun/Desktop/屏幕快照 2018-06-08 下午7.13.08.png)

   - config：包含该git仓库的特定配置。
   - info：包含一个全局排除性文件，指明在提交时，应该忽略哪些文件。
   - hooks：包含客户端和服务端的钩子脚本。

3. 在.git文件中，最重要的文件夹是HEAD、objects、refs和index。

   - objects：该目录下存储所有的数据内容。
   - refs：存储指向数据的提交对象的指针。
   - HEAD：指示目前被检出的分支。
   - index：保存暂存区信息。



### Git存储对象

> Git在存储数据时，会将数据转换位四种存储类型：
>
> 1. Object数据对象
> 2. Tree对象
> 3. Commit对象



- Object数据对象

  存储文件的内容，但不会存储文件的文件名信息。

- Tree对象

  树对象可以存储Object数据对象或Tree对象。在树对象中，每一个存储节点是一个Tree Entry，在Tree Entry中，包含该节点存储的数据内容的SHA-1指针以及数据类型。

- Commit对象

  在表现方式上可以理解为Commit对象能够将一次修改产生的Object对象、Tree对象打包放在一个新的Commit对象中。



### objects文件夹

> objects文件夹用来存储该Git仓库下的所有数据内容。在刚开始git init之后，里面是没有任何数据的



1. 创建一条数据，并存储。

   ```shell
   echo 'test content' | git hash-object -w --stdin
   ```

   使用这条命令会存储数据对象‘test content’。在存储时，Git会生成一个40位的字符校验和，这是一个SHA1哈希值（=SHA1（头部信息header+数据内容））

2. 查看ojects文件夹

   ```shell
   find .git/objects -type f
   ```

   输出信息为 ：

   ![ojects内容](/Users/yangkun/Desktop/屏幕快照 2018-06-08 下午7.26.04.png)

   校验和的前两个字符用来命名数据的存储目录，剩余的38个字符作为存储在目录中的文件名。

3. 可以使用cat-file命令从SHA1哈希值中解析出数据内容。

   ```shell
   git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
   ```

   输出结果：

   ![解析Git哈希值](/Users/yangkun/Desktop/屏幕快照 2018-06-08 下午7.33.57.png)

4. 在整个过程中，文件的每一个版本对应的SHA1值并不现实；其次，在保存时，保存的是文件内容，文件名并没有被保存。

