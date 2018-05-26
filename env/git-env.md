# Git环境配置

> 本次安装所用Git版本号：git.2.13.0

#####Git环境的安装

1. 从Git官方[网站下载](https://git-scm.com/download)地址下载对应版本的Git安装包，是dmg格式。
2. 下载完成后，双击该安装包根据提示进行安装。

#####Git环境的全局配置

1. 在GitHub上注册账号，并通过邮箱验证。

2. 使用刚刚注册的账号，配置Git全局用户名和用户邮箱。

3. git config -—global user.name “RunningYoungForJ”

   git config —-global user.email “yangkun.running@gmail.com”

   git config --list    可用于查看Git配置信息

   配置信息可以修改，以后想要更改就可以使用上面的指令重复执行一次

4. Git默认是大小写不敏感的，也就是说，将一个文件名某个字母做了大小写转换之后，Git是忽略这个改动的。导致会在同步代码的时候容易出现错误。所以建议把Git设置为大小写敏感。

5. git config core.ignorecase false    这句必须在一个git文件夹下执行.

6. 生成密钥。（Git关联远端仓库时候需要提供密钥，本地保存私钥，远端仓库用公钥来验证交互者身份）具体生成密钥操作如下：

   1. 生成ssh公钥私钥（如果没有.ssh文件夹，则需要先新建）生成密钥后，在本地的/Users/yangkun/.ssh/目录下会生成两个文件：id_rsa、id_rsa.pub。其中id_rsa文件保存私钥，保存于本地。id_rsa.pub文件保存公钥，需要将里面的内容上传到远端仓库。

      ```bash
      ssh-keygen -t rsa -C “yangkun.running@gmail.com”
      ```

   2. 进入.ssh文件夹，查看并复制ssh公钥.

      ```bash
      cd /Users/yangkun/.ssh/

      ls

      cat id_rsa.pub
      ```

   3. 将公约复制到GitHub远端仓库的个人信息的SSH and GPG keys设置中。设置完成后，可以使用以下命令验证。

      ```bash
      SSH -v git@github.com
      ```

#####本地关联远端仓库

1. 在本地创建一个文件夹作为本地Git仓库，在该文件夹下执行：

   ```bash
   git init    初始化文件夹，作为本地的一个仓库

   git remote add origin    通过ssh关联远端仓库

   git pull    将远端代码拉取到本地
   ```

   执行完毕后，远端仓库代码已经同步到本地！

​           