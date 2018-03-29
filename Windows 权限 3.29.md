### 前言 
前天学习了一下GitHub的 「markdown」（[markdown]:https://guides.github.com/features/mastering-markdown is a way to style text on the web.）的标记，才懂了之前读到的那些富文本的显示是怎么完成的，在markdown之前我还一直以为在GitHub的页面里面可以选择诸如Rich text
或者HTML的选项，直接将文本变成“所写即所得”的带style的格式。很早之前读李笑来的 Reborn系列 （地址请戳这里 https://github.com/xiaolai/reborn/blob/master/A04.md）
喜欢这种显示的格式，但是却不知道是怎么做出来的。现在懂了之后，就开始异常地喜欢用markdown配合文字来写。

今天继续深入了解Windows操作系统的知识，说来也是惭愧，使用了windows有十年了，作为IT从业人员也已经把windows作为自己的职业工具也有8年了，却也是从未有深刻的理解windows作为底层操作系统的运作方式和原理，仅仅也不过是一个因为用的多而有一些高级的“用户”而已。

# Windows 服务器提权和开启3389远程连接

**0x00 权限管理介绍**
Windows操作系统根据权限高低来决定用户在这台机器上能做的事情。比如有的文件规定了低权限用户是无法读写的，而这些文件通常是我们想要获取的敏感文件。有的文件夹是规定不能读写的，那么我们就不能上传任何文件到这个文件夹，也无法从这个文件里运行任何程序，所以我们连接上服务器都要找一个可读可写的文件夹来继续上传我们需要的程序，如开后门的程序。
一般的网站内容文件都存储在服务器权限比较低的文件夹里，所以即使我们使用上传了webshell，最多也只能够对网站所在的文件夹操作，而不能完整的控制整个服务器。所以我们需要以一个权限相当高的用户来访问该台服务器。
Windows中以「用户组」来分配权限，每个用户组有不用的权限，其中最高权限的组是Administrator组（管理员对计算机/域有不受限制的完全访问权），拥有对整个系统进行操作system权限。每个用户组下可以创建读个用户。

**0x00 大马和菜刀
**0x02**
**0x03巴西烤肉提提权**
* 新建一个用户
>　net user [username][password] /add
* 添加到Administrators用户组
>　net localgroup Administrators [username] /add
*  激活用户
>  net user [username] /active:yes
巴西烤肉是一个非常强劲的程序，它可以无视觉拒绝强制执行cmd命令，经常被用到提权中。
**0x04**
> 远程桌面协议(RDP, Remote Desktop Protocol)是一个多通道(multi-channel)的协议，让用户（客户端或称“本地电脑”）连上提供微软终机服务的电脑（服务器端或称“远程电脑”）。大部分的Windows都有客户端所需软件。_服务端电脑方面，默认听取送到TCP3389端口的数据_。 - 百度百科

这是一种非常方便的对服务器操作的方式，安全素养高的管理员会选择将3389端口关闭，甚至开启防火墙禁止任何开启3389的操作。

# Windows RDS 远程桌面服务

windows各个版本的远程桌面功能：Windows Server 2003和Windows Server 2008及以上是彻底的多用户操作系统。Linux和Unix也是完全的多用户操作系统，它们都支持多用户同时登陆。

Windows XP、Windows Vista、Windows 7、Windows 8仅仅是“准多用户”的操作系统。它们支持多用户登录，但是不能同时登录。更早的Windows 95、Windows 98是单用户操作系统。

**一、单用户远程**
开启远程登录，添加需要允许远程登录的用户
计算机->属性->远程
![alt text][logo]

[logo]:https://github.com/bakerX/Diary/blob/master/images/srdp.jpg "remote desktop"





