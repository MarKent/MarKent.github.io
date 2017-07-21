---
layout: post
title: 网络编程
category: iOS
tag: iOS,网络编程
---

## 概述

> 在移动互联网时代,几乎所有的应用程序都离不开网络,如QQ,微博,百度地图等,这些应用持续地通过网络进行数据更新,使应用保持着新鲜和活力.一旦没有了网络,应用就缺失了数据的变化,不管外观如何华丽,终究只是一具空壳.
网络编程就是一种实时更新应用数据的常用手段.

## 原理

网络编程,就是使用套接字来达到进程间通信目的的技术.

### 网络编程中的几个重要概念

 - 客户端(Client): 移动应用,如iOS,Android.
 - 服务器(Server): 为客户端提供服务,提供数据,提供资源的机器.
 - 请求(Request): 客户端向服务器索取数据的一种行为.
 - 响应(Resopnse): 服务器对客户端的请求作出的反应,一般指返回数据给客户端.

`大致过程`: 客户端想要访问数据,首先向服务器提交一个请求,用于告知服务器所请求的数据.服务器接收到请求后,根据请求到数据库查找响应的资源,无论服务器是否成功拿到资源,都会将结果返回给客户端.

`数据`: 网络上所有的数据都是二进制数据,并且以二进制流的形式从一个节点到另一个节点.

#### URL(Uniform Resource Locator)

即统一资源定位符,通过一个URL可以访问互联网上唯一的资源.一个完整的URL由协议,主机地址,端口号,路径四个部分组成,基本格式如:

`协议://主机地址:端口号/路径`

常见协议与端口号:

 - HTTP: 80 超文本传输协议 访问远程网络资源
 - HTTPS: 443 超文本传输安全协议 安全的SSL加密传输协议,访问远程网络资源
 - FTP: 20,21,990 文件传输协议  访问共享主机的文件资源
 - POP3: 110 邮局协议(版本3)
 - SMTP: 25 简单邮件传输协议
 - telnet: 23 远程终端协议

`IP`: 主机地址,Internet上每台电脑的编号,为便于记忆,用域名替代.

#### TCP/IP

Transmission Control Protocol/Internet Protocol,传输控制协议/因特网互联协议,
是一种网络通信协议,它规范了网络上的所有通信设备,尤其是一个主机和另一个主机之间的数据往来格式及传送方式.
下图是TCP/IP协议的分层和OSI七层协议经典架构模型的对比.
TCP/IP网络通信协议将协议分为四个层次.

![](http://oh08pyi2u.bkt.clouddn.com/OSITCPIP.png)

 - 应用层: 对应于OSI参考模型的高层,主要负责应用程序的协议,如HTTP,FTP等负责客户端和服务器之间数据有效交流的数据传输协议
 - 传输层: 对应OSI模型的传输层,负责为应用层实体提供端到端的通信功能.该层定义了两个主要的协议:面向连接的传输控制协议(TCP),面向非连接的用户数据报协议(UDP)
 - 网络互连层: 对应OSI的网络层,主要用于将传输的数据进行分组,并且将分组后的数据发送到目标计算机或网络.该层包含网际协议(IP),地址解析协议(ARP),互联网组管理协议(IGMP),互联网控制报文协议(ICMP)4个主要协议.其中IP是国际互联层最重要的协议,它提供一个不可靠,无连接的数据报传递服务.
 - 网络接口层: 对应OSI的数据链路层和物理层,负责监听数据在主机和网络之间的交换.

`面向连接的传输控制协议TCP`
面向连接是指正式通信前必须要与对方建立起连接,TCP传输控制协议是基于连接的协议,
即在正式收发数据前,必须和对方建立可靠连接.
 
`三次握手`一个TCP连接必须要经过"三次握手"才能建立起来,通信完成时需要拆除连接.

 - 1.客户端发送SYN报文给服务器端,进入SYN_SEND状态
 - 2.服务器端收到SYN报文,回应一个SYN+ACK报文,进入进入SYN_RECV状态
 - 3.客户端收到服务器端的SYN报文,回应一个ACK报文,进入Established状态

`四次挥手`终止一个TCP连接需要经过四次挥手.

 - 1.一个应用程序首先调用close,该端就执行了"主动关闭",于是该端的TCP发送一个FIN分节,表示数据发送完毕
 - 2.接收到FIN的另一端执行"被动关闭",这个FIN由TCP确认.
 - 3.一段时间后,接受这个FIN文件结束符的应用进程将调用close关闭它的套接字,这使得该接收的应用陈谷也发送一个FIN.
 - 4.主动关闭的那一端接收被动关闭那一端发来的FIN并进行确认.

`面向非连接的用户数据报协议UDP`
"面向非连接"是指正式通信前不必与对方先建立连接,不管对方状态如何均可以直接发送.与TCP相反 

`Socket`
在网络中,两个程序之间是通过一个双向的通信连接来实现数据交换的.这个连接的一端
称为一个Socket,又称"套接字",包含了终端的IP地址,端口和传输协议等信息,是系统提供的用于实现
网络通信的方法.Socket是对TCP/IP的封装,但它并不是一个协议,只是给程序员提供了一个发送消息的接口,程序员使用这个接口提供的方法来发送和接收消息.网络通信其实就是Socket之间的通信,数据在两个Socket之间通过IO传输.

大致三个步骤:
 
 - 创建一个Socket并建立连接
 - 发送和接收消息
 - 断开连接

### 数据安全--MD5算法

`用户安全登录有两个原则,一是不能在网络上传输用户隐私的明文,而是不能在本地存储用户隐私数据的明文.`

对于数据安全方面有多种解决方案,其中MD5加密算法广为采用.

`MD5`Message-Digest Algorithm 5,消息摘要算法第五版,是计算机安全领域广泛使用的一种散列函数,用于提供消息的完整性保护.通过对任意一个二进制数据抽取特征码,得到一个32个字符的定长字符串,其有以下特点:

 - 相同的字符串使用相同算法,每次加密的结果是固定的.
 - 根据最终输出的值,无法得到原始的明文,即过程是不可逆的.

#### MD5加密方案选择

`NSString+Hash`这个分类已经封装了关于MD5加密的方法.

`方案一` 直接使用MD5

`password = password.md5string;`

网上退出了破解MD5算法的工具,故这种加密方法不安全.

`方案二` MD5加盐

为了增加解密难度,提供了MD5加盐的方式.所谓"加盐",就是在明文密码的固定位置插入一个随机字符串,再直接调用md5string的方法,如:

```
static NSString salt = @"ABCabc123@#!";
password = [password stringByAppendingString:salt].md5string;
```

注意:salt字符串一定要足够复杂,否则并没有意义.这种方法使用较少.

`方案三` HMAC

直接调用`hamcMD5StringWithKey:`方法,通过传入的key,底层使用这个key对密码加密,再调用md5String方法,即方案二.这种方法安全级别高一些,但是对同一个字符串每次的结果是一样的,所以存在被暴力破解的风险.

`方案四` 时间戳密码

```
-(NSString *)timePassword {
    //1.生成key
    NSSting *key = @"key".md5String;
    //2.对密码进行hmac加密
    NSString *pwd = [self.password hmacMD5StringWithKey:key];
    //3.获取当前系统时间
    NSDateFormater *fmt = [[NSDateFormater alloc] init];
    fmt.local = [NSLocal localWithLocalIdentifier:@"zh"];//指定时区
    fmt.dateFormat = @"yyyy-MM-dd HH:mm";
    NSString *dateStr = [fmt stringFromDate:[NSDate date]];
    //4.将系统时间拼接在第一次加密密码的后面
    pwd = [pwd stringByAppendingString:dateStr];
    //5.返回再次hmac的结果
    return [pwd hmacMD5StringWithKey:key];
}
```

 - 用户注册时,客户端输入用户名user和密码password,由于服务器不会明文的记住用户的密码,故用户提交时的密码会采用HMAC的加密方式,服务器端的数据库会记录加密后的信息即password.hmac.
 
 - 用户登录的时候,客户端的密码依旧是password,为了与服务器保持一致,客户端先用HMAC对密码进行加密得到password.hamc,之后再拼接客户端的系统时间,得到一个新字符串,之后再对这个新字符串进行HAMC加密,最终得到"(password.hamc 2016-12-20 16:38).hamc"形式的加密密码.
 
 - 服务器根据用户名取出口令password.hamc,在对口令拼接服务器服务器端的时间,之后再进行HMAC加密,得到"(password.hmac 服务器时间).hamc"

 - 只要时间变化,密码就可能失效,为此服务器需要再次记录时间生成上述格式密码进行比较.
 
 这种加密方式安全性高,目前使用也广泛,但是需要服务器端的脚本支持,而且客户端和服务器端的时间不同步,易造成密码多次失效.

`方案五` 服务器时间戳密码

```
-(NSString *)timePassword {
    //1.生成key
    NSSting *key = @"key".md5String;
    //2.对密码进行hmac加密
    NSString *pwd = [self.password hmacMD5StringWithKey:key];
    //3.根据URL获取服务器时间
    NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:@"..."]];
    NSDictionary *dic = [NSJSONSerilization JSONObjectWithData:data options:0 error:NULL];
    NSString *timeStr = dic[@"timekey"];
    //4.将系统时间拼接在第一次加密密码的后面
    pwd = [pwd stringByAppendingString:timeStr];
    //5.返回再次hmac散列密码加密的结果
    return [pwd hmacMD5StringWithKey:key];
}
``` 

`解决了时间不同步的问题,是目前加密方法最好的选择.`

#### 钥匙串访问

MD5保存在本地的密码是不可逆的,用户从本地获取用户信息,密码是加过密的,会影响用户体验.苹果在iOS7.0.3之后加入了iCloud钥匙串功能. 钥匙串采用256位AES加密技术,保证了密码的安全,并且过程可逆,用户可以得到原始密码,增强用户体验.代码中一般可采用第三方框架sseychain.

`SSKeychain常用方法`

```
//获取所有账户
+(NSArray *)allAcounts:
//获取所有账户信息
+(NSArray *)accountsForService:(NSString *)serviceName;
//获取账户密码
+(NSArray *)passwordForService:(NSString *)serviceName account:(NSString *)account;
//删除账户密码
+(BOOL)deletePasswordForService:(NSString *)serviceName account:(NSString *)account;
//将账户密码保存在钥匙串
+(BOOL)setPassword:(NSString *)password forService:(NSString *)serviceName account:(NSString *)account;
```


