---
layout: post 
title: c++开源爬虫-Larbin简介
tags: [Search Engine]
---
因为最近学校实训，做的是一个搜索相关的项目，并且是c++的一个项目，所以就想到了larbin，于是接下来几天就先研究研究其源码，再根据项目需求修改其源码。

不多说，直接进入今天的正题。今天的目的就是简单了解下larbin。

## Larbin简介
   larbin是一种开源的网络爬虫/网络蜘蛛，由法国的年轻人Sébastien Ailleret独立开发，用c++语言实现。larbin目的是能够跟踪页面的url进行扩展的抓取，最后为搜索引擎提供广泛的数据来源。 Larbin只是一个爬虫，也就是说larbin只抓取网页，至于如何parse的事情则由用户自己完成。另外，如何存储到数据库以及建立索引的事情 larbin也不提供。 　

   latbin最初的设计也是依据设计简单但是高度可配置性的原则，因此我们可以看到，一个简单的larbin的爬虫可以每天获取500万的网页，实在是非常高效。 　利用larbin，我们可以轻易的获取/确定单个网站的所有联结，甚至可以镜像一个网站；也可以用它建立url 列表群，例如针对所有的网页进行 url retrive后，进行xml的联结的获取。或者是 mp3，或者定制larbin，可以作为搜索引擎的信息的来源。
   
Larbin官网：[http://larbin.sourceforge.net/index-eng.html](http://larbin.sourceforge.net/index-eng.html)
   
##How to use Larbin
由于Larbin开发环境是linux所以最好是在linux系统下执行下面的操作。

首先你肯定要下载larbin，可以当官网上下载，然后解压；也可以到我的github上下,命令如下。

> git clone https://github.com/SpeedMe/larbin.git

进入到目录下面，命令段执行下面的命令：

> ./configure
> make
> ./larbin

##如何按自己方式配置larbin
此时你已经配置好了,并且执行了./larbin命令运行起来了，你可以访问[http://localhost:8081/](http://localhost:8081/)了，但是你是按照官方默认的配置来运行该爬虫的，你也可以自己对其配置文件进行修改，你要修改的配置文件主要有两个options.h和larbin.conf。下面是我当网上找到的配置文件介绍。

每次修改larbin.conf后，不需要执行前两个命令，可以直接运行larbin即：./larbin
但是修改其他文件的时候就要三个命令都执行。

英文好的可以之间看官网上的配置文件介绍:[http://larbin.sourceforge.net/custom-eng.html](http://larbin.sourceforge.net/custom-eng.html)

最常用的修改是startUrl的值，即其实网页的值，你也可以定义多个startUrl。

```html
1、larbin.conf文件

###############################################
//客户端标记，当对其他网站抓取时，被抓取的网站知道是什么抓取的

UserAgent larbin_2.6.3

############################################
# What are the inputs and ouputs of larbin
# port on which is launched the http statistic webserver
# if unset or set to 0, no webserver is launched

//用于运行的http web服务器的端口号（larbin运行时访问http://localhost:8081/，设置为http_port 8081）.如果将端口设为0，则不会启动web服务器。通过这个可以查看爬行结果。

httpPort 8081

# port on which you can submit urls to fetch
# no input is possible if you comment this line or use port 0

//你要爬取url的端口。如果注释掉或设为0，则可能没有任何输入。如果通过手动或者程序提交爬取的//urls，则必须练就到计算机的TCP端口1976,即设为:inputPort 1976,可以添加爬行的url。

#inputPort 1976

############################################
# parameters to adapt depending on your network
# Number of connexions in parallel (to adapt depending of your network speed)
//并行爬取网页的数量，根据自己环境的网速调解，如果超时太多，则要降低这个并行数量

pagesConnexions 100

# Number of dns calls in parallel
//并行DNS域名解析的数量。

dnsConnexions 5

# How deep do you want to go in a site
//对一个站点的爬取的深度

depthInSite 5

# do you want to follow external links
//不允许访问外部链接。如果设置则只可访问同一主机的连接
#noExternalLinks

# time between 2 calls on the same server (in sec) : NEVER less than 30
//访问同一服务器的时间间隔。不可低于30s，建议60s
waitDuration 60

# Make requests through a proxy (use with care)
//是否用代理连接，如果用，则要设置、可以不用尽量不要用，这个选项要谨慎
#proxy www 8080

##############################################
# now, let's customize the search

# first page to fetch (you can specify several urls)
//开始爬取的URL
startUrl http://slashdot.org/

# Do you want to limit your search to a specific domain ?
# if yes, uncomment the following line
//这个选项设置了，则不可以爬行指定的特殊域名
#limitToDomain .fr .dk .uk end

# What are the extensions you surely don't want
# never forbid .html, .htm and so on : larbin needs them
//不想要的扩展名文件。一定不要禁止.html、.htm.larbin爬取的就是它们。禁止也是无效的
forbiddenExtensions
.tar .gz .tgz .zip .Z .rpm .deb
.ps .dvi .pdf
.png .jpg .jpeg .bmp .smi .tiff .gif
.mov .avi .mpeg .mpg .mp3 .qt .wav .ram .rm
.jar .java .class .diff
.doc .xls .ppt .mdb .rtf .exe .pps .so .psd
end

2、options.h

2.1 输出模式

// Select the output module you want to use

//默认模式。什么也不输出，不要选择这个
#define DEFAULT_OUTPUT // do nothing...
//简单保存，存在save/dxxxxx/fyyyyy文件中，每个目录下2000个文件
//#define SIMPLE_SAVE // save in files named save/dxxxxxx/fyyyyyy
//镜像方式存储。按网页的层次存储，可以作为网页的字典。
//#define MIRROR_SAVE // save in files (respect sites hierarchy)
//状态输出。在网页上进行状态输出，可以查看http://localhost:8081/output.html查看结果
//#define STATS_OUTPUT // do some stats on pages
    这些模式被定制在src/type.h中，可以在src/interf/useroutput.cc中定制自己的输出模式。
   这个文件中还有很多相关配置，更改后，需要重新编译。
  
2.2 特定查询

// Set up a specific search
//设置特定的查询
//#define SPECIFICSEARCH
//内容类型
//#define contentTypes ((char *[]) { "audio/mpeg", NULL })
//文件扩展。用于查询速度，不涉及类型，类型由上一个决定
//#define privilegedExts ((char *[]) { ".mp3", NULL })

2.3 设置完要设置特定文件的管理

#define DEFAULT_SPECIFIC //默认管理方式。 作为html有限制除了被解析。

// 存储特定文件。 允许将文件存储在硬盘上 文件可以很大在src/types.h 可以具体设置。
#define SAVE_SPECIFIC 

//动态存储模式。对于较大的文件动态的分配buffer。
#define DYNAMIC_SPECIFIC 
     可以通过"src/fetch/specbuf.cc" and "src/fetch/specbuf.h" 定义特定文件的管理方式。

2.4 你要爬虫做什么

//不继续子链接。不设置此项则html页不被解析链接也不会爬子链接。通过输入系统添加url时很有用
#define FOLLOW_LINKS

//每个网页中包含的子链接的列表。在"useroutput.cc" 用page->getLinks() 访问此信息。
#define LINKS_INFO 

//url标签。设置此项url有一个int(默认为0)。使用输入系统统时应该给定一个int。可以通过其获取u//rl。可以重定向。
#define URL_TAGS

//不允许重复。如果设置则遇到相同网页但已遇到过时则不管。
#define NO_DUP

//结束退出。没有url可爬取时是否退出。设置则退出。
#define EXIT_AT_END 

//抓取网页中的图片。设置了此项则要更新larbin.conf中禁止项。
#define IMAGES

//抓取任何类型网页不管其的类型。设置要更新larbin.conf。
#define ANYTYPE

//要larbin管理cookies。只简单实现但很有用。
#define COOKIES

2.5 其他选项说明

#define CGILEVEL 1        //定于选项及其参数。用于对爬行的url的限制。

#define MAXBANDWIDTH 200000  //larbin使用的带宽大小。不设置则不限带宽。

#define DEPTHBYSITE          //当url链接到其他站点时新rul的深度是已被初始化的。

2.6 效率和特征

//是否为输入制定一个专用线程。当你在useroutput.cc定义自己的代码时必须设置此项。
#define THREAD_OUTPUT

//重启位置记录表。设置此项时可以从上次终止处继续爬取。使用-scratch 选项从上次结束处重启。
#define RELOAD

2.7 Larbin怎么工作

#define NOWEBSERVER    //不启动服务器。不运行线程时很有用

#define GRAPH //是否在状态也使用柱状图。

#define NDEBUG //不启动调试信息。

#define NOSTATS //不启动状态输出。

#define STATS //启动状态输出。运行时每个一段时间就会输出抓取的状态。

#define BIGSTATS //在标准输出上显示每个被抓去的网页名字。会降低larbin速度

#define CRASH //用于报告严重的bugs用。以gmake debug模式编译时使用。
```

当你熟悉了上面的配置文件之后，你差不多就能玩转Larbin了，但是呢，有些仅仅一样东西毕竟难以所有人的意愿，所以就需要我们自己来修改源代码了，然后修改之前，我们肯定要先将larbin的代码给过一遍，对它的源码有一定研究之后再来改。

接下来的几天我就是现研究源码，然后修改。

乐此不疲～

2014-06-16 21:09:22
