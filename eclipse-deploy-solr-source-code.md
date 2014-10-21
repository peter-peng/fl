Title: eclipse部署solr源码调试
Date: 2013-12-02 20:48
Author: suhong
Category: 技术流
Slug: eclipse-deploy-solr-source-code

项目中需要需要对solr中的一些类进行定制，于是将solr源码部署在eclipse下调试。  
1.Apache官网下载solr源码，这里用的是4.6.0版本，解压到自己指定的目录；  
2.ant eclipse用ant工具生成eclipse加载需要的文件，即生成eclipse的工程  

如果本地没有安装ant，需先安装，Apache官网找，下载后解压，这里解压到了/opt/ant目录下，在/etc/profile中增加ANT\_HOME的环境变量：  
export ANT\_HOME=/opt/ant/apache-ant-1.9.2  
PATH=$PATH:$ANT\_HOME/bin  
export ANT\_HOME \#设不设置都行  
export PATH \#若有这一行无需添加  
保存后退出，执行source /etc/profile，ant
-version显示ant版本，ant安装成功。  
安装完ant后需要安装ivy，执行命令ant ivy-bootstrap，等待提示成功。  
现在到solr的源码的根目录下执行ant
eclipse命令了，ant会加载xml文件生成项目，这个过程ivy会下载所需的jar包，可能需要等一段时间，终端提示出现ivy-fail不用担心，只是所需依赖包没有，ivy会自动去下载，耐心等待，最后successful。  
3.eclipse导入solr源码，进行调试  

eclipse导入solr源码，找到StartSolrJetty.java，更改connector.setPort(8983)为设置的端口（这里是8983），bb.setContextPath("/solr")中参数说明在浏览器访问的URI路径，bb.setWar("webapp/web")这里设置solr源码下的solr/webapp/web目录的绝对路径。接下来设置solrhome，右键点击StartSolrJetty类，run
as选项下的run configurations选项，在Arguments中的VM
arguments中添加-Dsolr.solr.home=solrhome目录，run as
application启动，成功后在页面访问http://localhost:8983/solr即可访问到。  
solr源码中加入了jetty，所以run as application其实是在jetty中部署了solr。
