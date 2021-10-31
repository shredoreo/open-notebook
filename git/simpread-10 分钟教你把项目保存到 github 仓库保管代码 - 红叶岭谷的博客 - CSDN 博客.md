> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://blog.csdn.net/male09/article/details/72627815 [](http://creativecommons.org/licenses/by-sa/4.0/)版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。 本文链接：[https://blog.csdn.net/male09/article/details/72627815](https://blog.csdn.net/male09/article/details/72627815)

方法一
---

GitHub 是基于 git 实现的代码托管。git 是目前最好用的版本控制系统了，非常受欢迎，比之 svn 更好。  
强调内容  
GitHub 可以免费使用，并且快速稳定。即使是付费帐户，每个月不超过 10 美刀的费用也非常便宜。

利用 GitHub，你可以将项目存档，与其他人分享交流，并让其他开发者帮助你一起完成这个项目。优点在于，他支持多人共同完成一个项目，因此你们可以在同一页面对话交流

创建自己的项目，并备份，代码不需要保存在本地或者服务器，GitHub 做得非常理想。

学习 Git 也有很多好处。他被视为一个预先维护过程，你可以按自己的需要恢复、提交出现问题, 或者您需要恢复任何形式的代码，可以避免很多麻烦。Git 最好的特性之一是能够跟踪错误，这让使用 Github 变得更加简单。Bugs 可以公开，你可以通过 Github 评论，提交错误。

在 GitHub 页面，你可以直接开始，而不需要设置主机或者 DNS。

学习步奏开始 ============  
一、创建 github repository(仓库), 也就是你注册一个账号  
注册网站 [https://github.com/](https://github.com/)  
![](https://img-blog.csdn.net/20170522155600270?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  
点击 sign in—> 登录  
点击 sign up—> 注册  
![](https://img-blog.csdn.net/20170522155822914?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

填完个人信息后登录进入个人主界面如图

![](https://img-blog.csdn.net/20170522160033158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击新建仓库 **(一个仓库对应一个项目, 不能出现一对多)**

![](https://img-blog.csdn.net/20170522160358970?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![](https://img-blog.csdn.net/20170522160810490?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1.1 创建仓库完成

创建成功后，可以看到自己的仓库地址，如此，我的远程免费的仓库就创建了。它还介绍了 github 仓库的常用指令。这个指令需要在本地安装 git 客户端。

二、安装 Git 客户端  
官方下载地址：[http://git-scm.com/download/](http://git-scm.com/download/) 根据你自己的系统 下载对应版本  
选择安装组件，按默认的来就好了。

1）图标组件 (Addition icons) : 选择是否创建快速启动栏图标 或者 是否创建桌面快捷方式;

2）桌面浏览 (Windows Explorer integration) : 浏览源码的方法, 单独的上下文浏览 只使用 bash 或者 只用 Git GUI 工具; 高级的上下文浏览方法 使用 git-cheetah plugin 插件;

3）关联配置文件 (Associate .git*) : 是否关联 git 配置文件, 该配置文件主要显示文本编辑器的样式;

4）关联 shell 脚本文件 (Associate .sh) : 是否关联 Bash 命令行执行的脚本文件;

5）使用 TrueType 编码 : 在命令行中是否使用 TruthType 编码, 该编码是微软和苹果公司制定的通用编码;

![](https://img-blog.csdn.net/20170522161641456?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

设置环境变量 : 选择使用什么样的命令行工具, 一般情况下我们默认使用 Git Bash 即可, 默认选择;

1）Git 自带 : 使用 Git 自带的 Git Bash 命令行工具;

2）系统自带 CMD : 使用 Windows 系统的命令行工具;

3） 二者都有 : 上面二者同时配置, 但是注意, 这样会将 windows 中的 find.exe 和 sort.exe 工具覆盖, 如果不懂这些尽量不要选择;

![](https://img-blog.csdn.net/20170522161823833?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择换行格式 ，依然是默认就好。

1）检查出 windows 格式转换为 unix 格式 : 将 windows 格式的换行转为 unix 格式的换行在进行提交;

2）检查出原来格式转为 unix 格式 : 不管什么格式的, 一律转为 unix 格式的换行在进行提交;

3）不进行格式转换 : 不进行转换, 检查出什么, 就提交什么;

![](https://img-blog.csdn.net/20170522162030355?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择终端模拟器，依然默认就好

1）使用 MinTTY，就是在 Windows 开了一个简单模拟 Linux 命令环境的窗口 Git Bash

2）使用 windows 的系统的命令行程序 cmd.exe

![](https://img-blog.csdn.net/20170522162046964?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择默认就好，不用文件系统缓存

![](https://img-blog.csdn.net/20170522162102480?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2.1 安装成功完成。

三、绑定用户  
打开 git-bash.exe，在桌面快捷方式 / 开始菜单 / 安装目录中  
或者右击点击 Git Bash Here 进入界面

![](https://img-blog.csdn.net/20170522162723814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

因为 Git 是分布式版本控制系统，所以需要填写用户名和邮箱作为一个标识，用户和邮箱为你 github 注册的账号和邮箱

![](https://img-blog.csdn.net/20170522163142227?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

ps ： git config –global 参数，有了这个参数，表示你这台机器上所有的 Git 仓库都会使用这个配置，当然你也可以对某个仓库指定的不同的用户名和邮箱。

四、为 Github 账户设置 SSH key

众所周知 ssh key 是加密传输。

加密传输的算法有好多，git 使用 rsa，rsa 要解决的一个核心问题是，如何使用一对特定的数字，使其中一个数字可以用来加密，而另外一个数字可以用来解密。这两个数字就是你在使用 git 和 github 的时候所遇到的 public key 也就是公钥以及 private key 私钥。

其中，公钥就是那个用来加密的数字，这也就是为什么你在本机生成了公钥之后，要上传到 github 的原因。从 github 发回来的，用那公钥加密过的数据，可以用你本地的私钥来还原。

如果你的 key 丢失了，不管是公钥还是私钥，丢失一个都不能用了，解决方法也很简单，重新再生成一次，然后在 github.com 里再设置一次就行

4.1 生成 ssh key

首先检查是否已生成密钥 cd ~/.ssh，ls 如果有 3 个文件，则密钥已经生成，id_rsa.pub 就是公钥

![](https://img-blog.csdn.net/20170522163326699?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果没有生成，那么通过 $ ssh-keygen -t rsa -C “自己的邮箱地址” 来生成。

1）是路径确认，直接按回车存默认路径即可

2）直接回车键，这里我们不使用密码进行登录, 用密码太麻烦;

3）直接回车键

![](https://img-blog.csdn.net/20170522163456768?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

生成成功后，去对应目录用记事本打开 id_rsa.pub，得到 ssh key 公钥

![](https://img-blog.csdn.net/20170522163755658?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

4.2 为 github 账号配置 ssh key

切换到 github，展开个人头像的小三角，点击 settings  
然后打开 SSH and GPG keys 菜单， 点击 Add SSH key 新增密钥，填上标题，跟仓库保持一致吧，好区分。  
接着将 id_rsa.pub 文件中 key 粘贴到此，最后 Add key 生成密钥吧。

![](https://img-blog.csdn.net/20170522164210038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如此，github 账号的 SSH keys 配置完成。

### 五、上传本地项目到 github

5.1      git init // 把这个目录变成 Git 可以管理的仓库

5.2      git add [README.md](http://README.md) // 文件添加到仓库

5.2.1      git add . // 不但可以跟单一文件，还可以跟通配符，更可以跟目录。一个点就把当前目录下所有未追踪的文件全部 add 了

5.3      git commit -m “文件的简介” // 把文件提交到仓库  
5.4      git remote add origin [git@github.com](mailto:git@github.com):stepqian/Android-Bluetooth-Low-Energy.git // 关联远程仓库  
![](https://img-blog.csdn.net/20170522164752874?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFsZTA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

git push -u origin master // 把本地库的所有内容推送到远程库上

等待 100% 的完成后刷新你的 github 查看

方法二
---

在使用 studio 开发的项目过程中有时候我们想将项目发布到 github 上，以前都是用一种比较麻烦的方式（cmd）进行提交，最近发现 studio 其实是自带这种功能的，终于可以摆脱命令行了。

因为自己也没有做很深的研究，这里就先分享一下通过 studio 将自己的项目上传到 github 上的步骤。

两个相关概念：[Git](http://lib.csdn.net/base/git "Git知识库") 和 github

Git 是一个开源的分布式[版本控制](http://lib.csdn.net/base/git "Git知识库")系统，用以有效、高速的处理从很小到非常大的项目版本管理。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。分布式相比于集中式的最大区别在于开发者可以提交到本地，每个开发者通过克隆（git clone），在本地机器上拷贝一个完整的 Git 仓库。


github 作为开源代码库以及版本控制系统，它是一个网站, 给用户提供 git 服务. 这样你就不用自己部署 git 系统直接注册个账号, 就可以用他们提供的 git 服务。GitHub 可以托管各种 git 库，并提供一个 web 界面，GitHub 的独特卖点在于从另外一个项目进行分支的简易性。为一个项目贡献代码非常简单：首先点击项目站点的 “fork” 的按钮，然后将代码检出并将修改加入到刚才分出的代码库中，最后通过内建的 “pull request” 机制向项目负责人申请代码合并。


===============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================

### 准备

安装 Androidstudio 并新建一个工程；

安装 git 版本控制系统. 如 Git GUI；

在 github 网站上注册一个账号.

### 步骤

1 studio 的 git 配置；

安装好 git 后启动 Androidstudio，打开如下路径 File->Settings->Version Control(展开)->git

在 Path to Git executable 后面的输入框输入你安装的 git 路径，如下图所示：

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124105113171-1484753383.png)

点击 test 按钮如果出现 Git executed successfully 对话框说明配置成功，同时对话框会显示你安装的 git 版本号；如下图所示

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124105339765-461033048.png)

2 配置 github 登录信息；

打开如下路径 File->Settings->Version Control(展开)->GitHub，如下图所示

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124105638734-1503315165.png)

填入如下信息：

Host:github.com

Login: 你的 github 账户名

Password：你的 github 账户密码

填完之后点击 test 按钮，如果出现如下对话框说明配置成功，注意，新版的 git 的储存目录为 D:\Program Files\Git\cmd

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124105944546-1886822709.png)

3 上传工程到 github

打开你要上传的工程，顶部菜单选择 VCS->Import into Version Control->Share Project on GitHub, 如下图所示：

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124110443562-1476456479.png)

如果你是第一次提交该项目会出现如下对话框，提示你这是一个新的存储库（repo），可以自定义 repo 的名字，和添加描述。

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124111007046-1958470909.png)

填写完毕点击 share 按钮如果你的工程没有问题会出现如下界面

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124111132827-287766732.png)

这里列出了将要提交的类，以及各种资源配置文件等等，点击 ok 按钮

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124111419202-1161522475.png)

**注意**：这一步容易出现上传失败，究其原因，是没有在 git-bash 中进行配置：

今天博主正在愉快地学习在 AndroidStudio 中使用 [Git](http://lib.csdn.net/base/28 "Git知识库")，结果报了下面这个错∑(っ °Д°;) っ：

Can't finish GitHub sharing process

Successfully created project 'Demo' on GitHub, but initial commit failed:  

*** Please tell me who you are. Run [Git](http://lib.csdn.net/base/git "Git知识库") config --global user.email "you@example.com" git config --global user.name "Your Name" to set your account's default identity. Omit --global to set the identity only in this repository. fatal: empty ident name (for (null)>) not allowed during executing git -c core.quotepath=false commit -m "Initial commit" --

看了一下错误原因:Run git config --global user.email "you@example.com" git config --global user.name "

原来是 git 没有配置的原因，找到 git 安装目录下的 Git Bash 运行后输入下面两行代码即可：

1.  git config --global user.email "you@example.com"  
2.  git config --global user.name "Your Name"

问题解决接着继续：

输入你的 Master password 点击 ok，如果提交成功 studio 右上角会提示相关信息

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124111644499-2115751566.png)

此时打开你的 github 网站地址在你的 repositories 中会看到刚刚提交过的工程名称，点击进去会看到完整的提交工程，到此提交结束

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124111930921-1815976777.png)

项目更新

当项目新增了模块或者模块修改了如何更新 github 上的项目，其实也很简单。

1 如果你的项目新增了一个类，当你创建该类的时候会提示你是否需要加入 git，如下图所示

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124112340921-1874847582.png)

选择 yes 该类就会加入 git，同时该类本身的颜色会有改变（Darcula 主题下由正常的白色变为绿色）

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124112813546-135874046.png)

此时该类右击 ->Git->COmmit File... 出现如下对话框

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124113049109-41420681.png)

填写 commit message 后点击 Commit 按钮，有可能会出现如下警告，忽略它点击 Commit

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124113253734-1422945803.png)

再次右击 ->Git->Repository->Push, 如下图所示

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124113518390-1823912880.png)

点击 Push 出现如下对话框，点击 Pust 按钮

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124113718374-214611476.png)

此时打开你的 github 上的该项目源码，你会发现新增的类已经出现了

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124113908046-1374882837.png)

 2 如果你的项目中某个类进行了修改需要重新提交；

 右击该类 ->Git-Add

![](https://images2015.cnblogs.com/blog/828272/201511/828272-20151124114511374-47909326.png)

感觉这步没什么变化？其实不是，这步其实是吧该类加入到 git 中；

以后的步骤和新增类的操作一样，这里不再赘述。

================== 完成结束 ==================