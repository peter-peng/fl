Title: 解决mongoexport导出数据时NumberLong问题
Date: 2014-10-08 09:00
Author: neoyin
Category: 技术流
Tags: mongodb
Slug: mongodb-import-data-longnumber


### Mongdb导出工具mongoexport命令：

```
[root@localhost mongodb]# ./bin/mongoexport -h

Export MongoDB data to CSV, TSV or JSON files.

options:
  --help                    produce help message
  -v [ --verbose ]          be more verbose (include multiple times for more
                            verbosity e.g. -vvvvv)
  --version                 print the program's version and exit
  -h [ --host ] arg         mongo host to connect to ( <set name>/s1,s2 for
                            sets)
  --port arg                server port. Can also use --host hostname:port
  --ipv6                    enable IPv6 support (disabled by default)
  -u [ --username ] arg     username
  -p [ --password ] arg     password
  --dbpath arg              directly access mongod database files in the given
                            path, instead of connecting to a mongod  server -
                            needs to lock the data directory, so cannot be used
                            if a mongod is currently accessing the same path
  --directoryperdb          if dbpath specified, each db is in a separate
                            directory
  --journal                 enable journaling
  -d [ --db ] arg           database to use
  -c [ --collection ] arg   collection to use (some commands)
  -f [ --fields ] arg       comma separated list of field names e.g. -f
                            name,age
  --fieldFile arg           file with fields names - 1 per line
  -q [ --query ] arg        query filter, as a JSON string
  --csv                     export to csv instead of json
  -o [ --out ] arg          output file; if not specified, stdout is used
  --jsonArray               output to a json array rather than one object per
                            line
  -k [ --slaveOk ] arg (=1) use secondaries for export if available, default
                            true
  --forceTableScan          force a table scan (do not use $snapshot)

参数说明：
-h:指明数据库宿主机的IP
-u:指明数据库的用户名
-p:指明数据库的密码
-d:指明数据库的名字
-c:指明collection的名字
-f:指明要导出那些列
-o:指明到要导出的文件名
-q:指明导出数据的过滤条件
```
<!-- more -->

### Mongodb导入工具mongoimport

```
[root@localhost mongodb]# ./bin/mongoimport --help  
options:  
  --help                  produce help message  
  -v [ --verbose ]        be more verbose (include multiple times for more   
                          verbosity e.g. -vvvvv)  
  --version               print the program's version and exit  
  -h [ --host ] arg       mongo host to connect to ( <set name>/s1,s2 for sets)  
  --port arg              server port. Can also use --host hostname:port  
  --ipv6                  enable IPv6 support (disabled by default)  
  -u [ --username ] arg   username  
  -p [ --password ] arg   password  
  --dbpath arg            directly access mongod database files in the given   
                          path, instead of connecting to a mongod  server -   
                          needs to lock the data directory, so cannot be used   
                          if a mongod is currently accessing the same path  
  --directoryperdb        if dbpath specified, each db is in a separate   
                          directory  
  --journal               enable journaling  
  -d [ --db ] arg         database to use  
  -c [ --collection ] arg collection to use (some commands)  
  -f [ --fields ] arg     comma separated list of field names e.g. -f name,age  
  --fieldFile arg         file with fields names - 1 per line  
  --ignoreBlanks          if given, empty fields in csv and tsv will be ignored  
  --type arg              type of file to import.  default: json (json,csv,tsv)  
  --file arg              file to import from; if not specified stdin is used  
  --drop                  drop collection first   
  --headerline            CSV,TSV only - use first line as headers  
  --upsert                insert or update objects that already exist  
  --upsertFields arg      comma-separated fields for the query part of the   
                          upsert. You should make sure this is indexed  
  --stopOnError           stop importing at first error rather than continuing  
  --jsonArray             load a json array, not one item per line. Currently   
                          limited to 4MB.  
参数说明：
-h:指明数据库宿主机的IP
-u:指明数据库的用户名
-p:指明数据库的密码
-d:指明数据库的名字
-c:指明collection的名字
-f:指明要导入那些列
```

利用mongodbimport导入时有一些很蛋疼的问题，对于一些Number型的数据导入后并非NumberLong,导致程序中一些错误。
最新版的mongodb中只有当number大于等于8位时才会自动转为NumberLong.

解决这一问题的技巧如下
```
根据条件将一些数据查询出来并update
db.foo.find( { z : { $type : 16 } } ).forEach( function(o) { o.z = NumberLong(o.z); db.foo.save(o); } )
```
