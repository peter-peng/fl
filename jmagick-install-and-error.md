Title:  jmagick 常见错误和安装 
Date: 2014-07-12 02:55
Author: chengz
Category: 技术流
Tags: jmagick,ubuntu
Slug: jmagick-install-and-error

jmagick 常见错误和安装
======================

错误
----

- 找不到库文件

    Exception in thread "main" java.lang.UnsatisfiedLinkError: no JMagick in java.library.path

这个错误, 在/usr/lib64 下同时放了一个 `libJMagick.so` 和
`libJMagick-6.5.7.so` 才不报错了, 去掉哪一个都不行, 哪一个换成
`libJMagick-6.4.0.so`也不行. \>\_< \*因为 libJMagick.so
是指向libJMagick-6.5.7.so的一个软链接\*

- java.lang.NoClassDefFoundError: Could not initialize class magick.ImageInfo
- java.lang.UnsatisfiedLinkError
- java.lang.UnsatisfiedLinkError: /usr/lib/libJMagick.so:
libMagickCore.so.3: cannot open shared object file: No such file or
director;  
ldconfig /usr/local/lib

- Unable to find ImageMagick header files

apt-cache search libMagick  
sudo apt-get install libmagickcore-dev

- libMagick.so.10: cannot open shared object file: No such file or directory

[Linux安装JMagick](http://blog.csdn.net/velsnqi/article/details/7452479)  
http://downloads.jmagick.org/6.4.0/

- magick.MagickApiException: NoDecodeDelegateForThisImageFormat

显示ImageMagick支持的类型

    identify -list format

[NoDecodeDelegateForThisImageFormat的处理](http://linux.chinaunix.net/techdoc/desktop/2009/05/22/1114138.shtml)

### identify: error while loading shared libraries: libMagickCore.so.6: cannot open shared object file: No such file or directory

安装
----

下载 : [jmagick github](https://github.com/techblue/jmagick) ,
刚好最新版本ImageMagick打不开, 先下一个6.4.0的

    git clone https://github.com/techblue/jmagick.git
    wget http://downloads.jmagick.org/6.4.0/ImageMagick-6.4.0-0.tar.gz

安装 ImageMagick 比较顺利, 可能会有一些图片类型不支持,
参照[Linux安装JMagick](http://blog.csdn.net/velsnqi/article/details/7452479)有些需要提前装的.

    ./configure && make && make install

安装 JMagick

    ./configure && make && make install

也挺顺利, 生成的lib目录中 *libJMagick.so
是指向libJMagick-6.X.X.so的一个软链接* , 把它们copy到
"java.library.path" 中, (例如cp 到 /usr/lib64中), jmagick-6.x.x.jar就 cp
到 你的java classpath就可以了.

### 安装后的问题

因为前面没有安装一些需要的支持, 可能很多图片的格式都不能支持, 看前面
`./configure`的输出结果有哪些检测失败的报告.  
重新安装一次  
安装依赖, 下面的安装命令是centos的, 相关别的平台用自己的安装包软件来装

    yum install libpng 
    yum install libpng-devel 
    yum install libjpeg 
    yum install libjpeg-devel 
    yum install gd 
    yum install gd-devel 
    yum install libtiff 
    yum install libtiff-devel

然后再重来一遍上面的安装过程就O了.

refs:  

[Linux安装JMagick](http://blog.csdn.net/velsnqi/article/details/7452479)  

[NoDecodeDelegateForThisImageFormat的处理](http://linux.chinaunix.net/techdoc/desktop/2009/05/22/1114138.shtml)

发现一个链接已经挂了, 还没挂的附上原文:

> [NoDecodeDelegateForThisImageFormat的处理](http://linux.chinaunix.net/techdoc/desktop/2009/05/22/1114138.shtml)  
>  今天在移植ImageMagick++ 时遇到了下面的错误  
>  “NoDecodeDelegateForThisImageFormat”
>
> 移植方式是，只把要用的 libMagickCore.so.2 libMagick++.so.2
> libMagickWand.so.2 和 libjpeg.so.62
> \>拷贝过去，运行程序，后出现"NoDecodeDelegateForThisImageFormat"错误。  
>
> 最初以为是so动态库没有拷全，用ldd查看，发现都是全的，程序在原来的开发服务器是能用的（开发服务器是从源码安装的）。
>
> 网上查了一下，发现了这个  
>
> http://forums.fiveruns.com/forums/fiveruns-install/topics/imagemagick-nodecodedelegateforthisimageformat  
>  。
>
> “spcoder,If you update the ImageMagick env variables in
> \~/rminstall-1.1-0/scripts/setenv.sh to read as the following, it will
> enable the libraries:  
>  IMAGEMAGICK ENV \#\#\#\#\#export
> MAGICK\_HOME=”/opt/rminstall-1.1-0/common” export
> MAGICK\_CONFIGURE\_PATH=”/opt/rminstall-1.1-0/common/lib/ImageMagick-6.3.6/config:/opt/rminstall-1.1-0/common/share/ImageMagick-6.3.6/config”
> export
> MAGICK\_CODER\_MODULE\_PATH=”/opt/rminstall-1.1-0/common/lib/ImageMagick-6.3.6/modules-Q16/coders/”
> I get a large set of formats returned after making \>this updating and
> re-exporting these, when running your test command:$ identify -list
> formatBest,Mark ”  
>  应该是说 "环境变量"的事情
> ,他说要设置下环境变量，并用identify测试（这句很关键）。  
>  我运行了一下identify -list format 确实什么格式信息也没有。  
>  没办法hack 一下吧，在开发机上用  
>  strace identify -list format \>ImageMgick.log 2\>&1  
>
> 分析生成的log文件，里面有去到/usr/local/lib/ImageMagick-6.5.2/modules-Q16/coders这个目录下读取文件的记录。进去以后发现里面都是coders。原来它是需要这些文件啊。把这个目录都拷贝到目标机器上，然后看上面这些环境变量中有个：MAGICK\_\>CODER\_MODULE\_PATH  
>  把它export 出来（export MAGICK\_CODER\_MODULE\_PATH=）  
>  再运行，OK啦。  
>  Thank Strace！！！！！  
>  后记：在用ImageMagick++ 中的Magick++ library
> 创建缩略图的时候，发现在默认情况下调用Image::resize（Geometry
> ），他总是按照原图的比例来缩放，也就是说如果原图是800x600的图片，我设置的缩略图尺寸为100x100的话，那么生成的缩略图的尺寸为100x75
> ，也就是说是按照原图的宽高比来缩放。经查找后发现，如果想要100x100的缩略图的话，需要调用Geometry对象的aspect（）方法，并把传入ture参数：Geometry：：aspect（true），然后再去调用r\>esize（Geometry）就可以了。
>
> 为了找这个破东西，找到半夜两点，差点以为是Magick++不支持这个方法，郁闷
