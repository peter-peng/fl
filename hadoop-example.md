Title: hadoop学习例子
Date: 2013-11-19 00:04
Author: fuqiang
Category: 技术流
Tags: hadoop
Slug: hadoop-example


那啥，最近在绞尽脑汁学hadoop，因此在学习例子的同时，我会把我所学的例子贴上来，供看到的和没有看到帖子的人类学习。今天来个最简单的(我的配置是三台虚拟机，自己的配置按照自己情况来写url)
```
public class FileSystemCat { /\*\* \* @param args \* @throws IOException
\*/ public static void main(String[] args) throws IOException { // TODO
Auto-generated method stub String url =
"hdfs://192.168.13.134:49000/user/hadoop/input/core-site.xml";
//hdfs://192.168.13.134:49000这个地址是你在core-site.xml中配置的,user/hadoop是hadoop默认的根目录
Configuration conf = new Configuration(); FileSystem fs =
FileSystem.get(URI.create(url),conf); //初始化一个文件系统 InputStream
is = fs.open(new Path(url));
//将读取到的文件地址传入open方法来读取文件内容 IOUtils.copyBytes(is,
System.out, 4096, false); //打印文件内容 } }
```