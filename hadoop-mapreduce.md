Title: 打造分布式文件系统-Mapreduce
Date: 2013-11-01 12:05
Author: neoyin
Category: 技术流
Tags: hadoop
Slug: hadoop-mapreduce

前面进行了[Hadoop配置安装](http://www.floatinglife.cn/hadoop-install)

以及了解了[HDFS](http://www.floatinglife.cn/hadoop-hdfs)

接下来就是Mapreduce了.其概念就是Map（映射）Reduce（化简）主要思想，都是从函数式编程语言里借来的，以及从矢量编程语言里借来的特性.

<!--more-->

相关的原理咱先忽略,直接进入实例应用.

从hadoop源码中hadoop-2.2.0-src/hadoop-mapreduce-project/hadoop-mapreduce-examples/
打开其示例程序WordCount如下:

    public class WordCount {

      public static class TokenizerMapper 
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer 
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable values, 
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount  ");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

分为三步:

１.TokenizerMapper 类实现 Mapper 接口中的 map 方法，输入参数中的 value
是文本文件中的一行

2.IntSumReducer类实现 Reducer 接口中的 reduce 方法, 输入参数中的 key,
values 是由 Map 任务输出的中间结果，values 是一个 Iterator, 遍历这个
Iterator, 就可以得到属于同一个 key 的所有 value. 此处，key
是一个单词，value 是词频。只需要将所有的 value
相加，就可以得到这个单词的总的出现次数。

3.配置 Job并运行

就这么简单的三步,实现了分布式统计一批文本文件中单词出现的频率的功能.

<a name="table1"></a>**JobConf 常用可定制参数**

<table summary="JobConf 常用可定制参数" width="100%" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<th scope="col">
参数

</th>
<th scope="col">
作用

</th>
<th scope="col">
缺省值

</th>
<th scope="col">
其它实现

</th>
</tr>
<tr>
<th scope="row">
**InputFormat**

</th>
<td>
将输入的数据集切割成小数据集 InputSplits, 每一个 InputSplit 将由一个
Mapper 负责处理。此外 InputFormat 中还提供一个 RecordReader 的实现,
将一个 InputSplit 解析成 <key,value\> 对提供给 map 函数。

</td>
<td>
TextInputFormat  
(针对文本文件，按行将文本文件切割成 InputSplits, 并用 LineRecordReader
将 InputSplit 解析成 <key,value\> 对，key 是行在文件中的位置，value
是文件中的一行)

</td>
<td>
SequenceFileInputFormat

</td>
</tr>
<tr>
<th scope="row">
**OutputFormat**

</th>
<td>
提供一个 RecordWriter 的实现，负责输出最终结果

</td>
<td>
TextOutputFormat  
(用 LineRecordWriter 将最终结果写成纯文件文件,每个 <key,value\>
对一行，key 和 value 之间用 tab 分隔)

</td>
<td>
SequenceFileOutputFormat

</td>
</tr>
<tr>
<th scope="row">
**OutputKeyClass**

</th>
<td>
输出的最终结果中 key 的类型

</td>
<td>
LongWritable

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**OutputValueClass**

</th>
<td>
输出的最终结果中 value 的类型

</td>
<td>
Text

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**MapperClass**

</th>
<td>
Mapper 类，实现 map 函数，完成输入的 <key,value\> 到中间结果的映射

</td>
<td>
IdentityMapper  
(将输入的 <key,value\> 原封不动的输出为中间结果)

</td>
<td>
LongSumReducer,  
LogRegexMapper,  
InverseMapper

</td>
</tr>
<tr>
<th scope="row">
**CombinerClass**

</th>
<td>
实现 combine 函数，将中间结果中的重复 key 做合并

</td>
<td>
null  
(不对中间结果中的重复 key 做合并)

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**ReducerClass**

</th>
<td>
Reducer 类，实现 reduce 函数，对中间结果做合并，形成最终结果

</td>
<td>
IdentityReducer  
(将中间结果直接输出为最终结果)

</td>
<td>
AccumulatingReducer, LongSumReducer

</td>
</tr>
<tr>
<th scope="row">
**InputPath**

</th>
<td>
设定 job 的输入目录, job 运行时会处理输入目录下的所有文件

</td>
<td>
null

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**OutputPath**

</th>
<td>
设定 job 的输出目录，job 的最终结果会写入输出目录下

</td>
<td>
null

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**MapOutputKeyClass**

</th>
<td>
设定 map 函数输出的中间结果中 key 的类型

</td>
<td>
如果用户没有设定的话，使用 OutputKeyClass

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**MapOutputValueClass**

</th>
<td>
设定 map 函数输出的中间结果中 value 的类型

</td>
<td>
如果用户没有设定的话，使用 OutputValuesClass

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**OutputKeyComparator**

</th>
<td>
对结果中的 key 进行排序时的使用的比较器

</td>
<td>
WritableComparable

</td>
<td>
</td>
</tr>
<tr>
<th scope="row">
**PartitionerClass**

</th>
<td>
对中间结果的 key 排序后，用此 Partition 函数将其划分为R份,每份由一个
Reducer 负责处理。

</td>
<td>
HashPartitioner  
(使用 Hash 函数做 partition)

</td>
<td>
KeyFieldBasedPartitioner PipesPartitioner

</td>
</tr>
</tbody>
</table>
咱们以后再研究其内部实现及原理

参考资料:

[http://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop2/](http://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop2/)

[http://www.infoq.com/cn/articles/MapReduce-Best-Practice-1](http://www.infoq.com/cn/articles/MapReduce-Best-Practice-1)

[http://hadoop.apache.org/docs/r0.19.1/cn/mapred\_tutorial.html\#%E7%9B%AE%E7%9A%84](http://hadoop.apache.org/docs/r0.19.1/cn/mapred_tutorial.html#%E7%9B%AE%E7%9A%84)

[http://www.cnblogs.com/xia520pi/archive/2012/06/04/2534533.html](http://www.cnblogs.com/xia520pi/archive/2012/06/04/2534533.html)

[http://blog.csdn.net/kauu/article/details/1815353](http://blog.csdn.net/kauu/article/details/1815353)

[http://sishuok.com/forum/blogPost/list/0/5965.html](http://sishuok.com/forum/blogPost/list/0/5965.html)
