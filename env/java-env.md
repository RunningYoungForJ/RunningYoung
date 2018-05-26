# Java环境配置

> 本次安装所用Java版本号：jdk-8u131

#####Java环境的安装

1. 从Oracle Java官方网站[下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载需要安装的Java版本。是以.dmg的形式保存。（注意：这里有可能不能用迅雷等第三方下载器下载，会报错。因此最好用浏览器自带下载工具，本文所用为chrome）
2. 下载完成后，双击该.dmg格式安装包，根据提示自动完成Java的安装。
3. 默认安装路径：/Library/Java。
4. 各文件夹介绍：
   1. Contents下的Home文件夹是该JDK的根目录。
   2. Bin目录下存放JDK用于开发的一些终端命令工具。如常见的Javac（将Java源代码便衣为class文件）、java（运行class文件）。
   3. Db目录是Java开发的一个开源的关系型数据库。
   4. include目录是一些C语言的头文件。
   5. Jre目录下是JDK所依赖的java运行时文件。
   6. Lib目录下存放JDK开发工具所依赖的一些库文件。
   7. Man目录下存放JDK开发工具的说明文档。

##### Java环境的配置

1. 打开Terminal终端。

2. 如果第一次配置环境变量，需要在当前路径下create一个.bash_profile的隐藏配置文件。如果已经存在这个文件，则直接打开即可。

3. touch .bash_profile    创建一个隐藏配置文件

   open -e .bash_profile    打开隐藏配置文件

4. 打开.bash_profile文件之后，输入以下配置内容：

5. JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home（修改路径为具体jdk版本文件的路径）

   ```bash
   JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home

   PATH=JAVA_HOME/bin:PATH:.

   CLASSPATH=JAVA_HOME/lib/tools.jar:JAVA_HOME/lib/dt.jar:.

   export JAVA_HOME

   export PATH

   export CLASSPATH
   ```

6. 保存，并使使配置生效。

   ```bash
   source ~/.bash_profile
   ```

7. 最后输入java -version查看安装信息，显示成功则说明安装配置已经完成。

