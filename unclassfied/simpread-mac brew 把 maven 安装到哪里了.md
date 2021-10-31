> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/230e0b5de44e/)

> 用 brew 安装了 maven，maven 使用时，发现从 maven 的官方中央仓库更新依赖库速度太慢，所以想更改 maven 配置文件，添加国内的 maven 仓库镜像。但是，却不知道 brew 把 maven 装到...

用 brew 安装了 maven，maven 使用时，发现从 maven 的官方中央仓库更新依赖库速度太慢，所以想更改 maven 配置文件，添加国内的 maven 仓库镜像。但是，却不知道 brew 把 maven 装到哪里了。so，开始捉迷藏～

brew 把 maven 藏到哪里了呢？

既然是 brew 把 maven 藏起来的，那么首先想到，brew 是不是知道呢？我们就直接问问 brew。

在 bash 终端中

> $ brew  

> Example usage:
> 
> brew search [TEXT|/REGEX/]
> 
> brew (info|home|options) [FORMULA...]
> 
> brew install FORMULA...
> 
> brew update
> 
> brew upgrade [FORMULA...]
> 
> brew uninstall FORMULA...
> 
> brew list [FORMULA...]
> 
> Troubleshooting:
> 
> brew config
> 
> brew doctor
> 
> brew install -vd FORMULA
> 
> Developers:
> 
> brew create [URL [--no-fetch]]
> 
> brew edit [FORMULA...]
> 
> http://docs.brew.sh/Formula-Cookbook.html
> 
> Further help:
> 
> man brew
> 
> brew help [COMMAND]
> 
> brew home

大概看了下，下面这两个是最有可能提供线索的

brew search [TEXT|/REGEX/]  

brew (info|home|options) [FORMULA...]

info 提供了一条路径线索

> $ brew info maven  

> maven: stable 3.3.9
> 
> Java-based project management
> 
> https://maven.apache.org/
> 
> Conflicts with: mvnvm
> 
> **/usr/local/Cellar/maven/3.3.9 (96 files, 9.6M) ***
> 
> Built from source on 2017-01-21 at 15:32:59
> 
> From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/maven.rb
> 
> ==> Requirements
> 
> Required: java ✔

看看这个路径是 maven 的藏身之处吗？

> $ ls /usr/local/Cellar/maven/3.3.9  

> INSTALL_RECEIPT.json  NOTICE                bin/
> 
> LICENSE              README.txt            libexec/

大概是，为啥说大概，因为没看到 conf/setting.xml 文件啊！4 个文件和 2 个文件夹，setting.xml 会在 2 个文件夹里吗？  

> $ ls bin/  

> mvn*          mvn.cmd*      mvnDebug*    mvnDebug.cmd* mvnyjp*

bin / 下没有，虽然我也不知道为啥这些文件最后会带个 * 号，但明显不是我要找的。

> $ ls libexec/  

> bin/  boot/ conf/ lib/

啊哈，看我发现了什么？conf/ 那么 setting.xml 会在 conf 下吗？

> $ ls libexec/conf  

> logging/          settings.xml      toolchains.xml

终于找到你啦，setting.xml 君！

* * *

实际情况是：我在 “/usr/local/Cellar/maven/3.3.9” 目录下只查看了 bin 文件夹，libexec 文件夹被我忽视了，然后我废了好长时间在网上进行查找，但都没有找到相关的信息，一度绝望，然后决定从 maven 官网下载 maven 手动安装！

前往 maven 主页

> $ brew home maven  

![](http://upload-images.jianshu.io/upload_images/1830362-c7d7e18c7b693b14.jpg)

进到 install 页面（表问我为啥不先去 Download 页面下载 maven =_=，我也不知道，可能那一刻被大能控制了！），然后就看到 Maven home！！！

![](http://upload-images.jianshu.io/upload_images/1830362-2fc1e5411d8329d0.png)

总结：brew 和 maven 都知道 maven 被藏到哪里了！