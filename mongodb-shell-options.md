Title: mongodb  shell 操作
Date: 2013-11-01 17:09
Author: chengz
Category: 技术流
Tags: mongodb
Slug: mongodb-shell-options

## mongodb shell and script 

### 导入mongo javascript mongo

localhost:27017/test myjsfile.js 这个操作会先在mongo shell中登录localhost:27017的test数据库, 然后执行myjsfile.js.也可以在mongoshell中执行js文件,用load()方法,可以传入相对路径或绝对路径,假设mongoshell的工作目录是/data/db,
那么要执行/data/db/script/myjstest.js,可以用下面两种方法

```
load("script/myjstest.js") 
load("/data/db/script/myjstest.js") 
```
### 直接执行js mongo test --eval 

 "printjson(db.getCollectionNames())"这条命令会连接localhost:27017上的test数据库, 并在shell中执行传入的js

### 连接数据库 
```
new Mongo() new Mongo(<host>) new Mongo(<host:port>)
```
### 得到数据库实例 
```
conn = new Mongo() db = conn.getDB("myDatabase") 也可以 db = connect("localhost:27017/myDatabase") 
```
### 查询数据库数据,更新
```
var id = 123; var temp = db.ttt.findOne({type:id}); if(temp !=
null){
db.ttt.update({type:id},{$set:{commentId:NumberLong(temp.commentId),type:NumberLong(id)}});
}
```
### 数据集合遍历并操作 
```
db.ttt.find({type:123}).forEach( function(x){
// or other operations print(x); } ) 
```
### 查看帮助 控制台输入 help 
- 查看可以在db对象上执行的方法 db.help()
- 查看可以在collection对象上执行的方法 db.collectiion.help()
