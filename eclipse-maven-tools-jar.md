Title: eclipse中pom报错 tools.jar
Date: 2013-12-12 17:42
Author: chengz
Category: 技术流
Tags: maven
Slug: eclipse-maven-tools-jar

### eclipse中pom报错 tools.jar

> Missing artifact com.sun:tools:jar:1.5.0:system

原因，ecliple的java跟想用的java不是一个版本  
```
$ which java  
/usr/bin/java -\> /usr/lib/jvm/java-6-openjdk-amd64/jre/bin/java  
这个jre/../lib 下没有tools.jar
```

把/usr/bin/java重新链接到自己安装的jre下，重启eclipse就可以了

老外说：  
> http://stackoverflow.com/questions/5756299/maven-3-artifact-problem
What I have learned is that eclipse by default will use your system jre
to start eclipse. You probably have seen a message when starting eclipse
similar to "Eclipse is running under a JRE and m2eclipse requires a JDK
some plugins will not work"

> If you go to (in eclipse) Help -\> Installation Details and look for a
-vm you will probably see it pointing to somewhere that does not have
the path structure that it is expecting.

> Note: For whatever reason when I encountered this issue java.home in
maven was evaluated from where eclipse was launched from. So when it
tries to pull the tools.jar from what it sees as java.home it may not be
what you actually set as JAVA\_HOME as an env/system variable.
