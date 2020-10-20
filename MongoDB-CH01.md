<https://gitee.com/komavideo/LearnMongoDB/tree/master/> 

# 一、什么是MongoDB

```powershell
MongoDB是一个面向文档的免费数据库，多用于数据采集和分散处理（Map/Reduce），特别是在大数据处理方便比较擅长。
Spark
数据库引擎排行：https://db-engines.com/en/ranking

面向文档的数据库
面向关系的数据库
	Oracle
	SQLServer
	MySQL
	PostgreSql
NoSql
	MongoDB
	Redis
	
官方网站：https://www.mongodb.com
	http://dl.mongodb.org/dl/win32/x86_64
```

# 二、MongoDB的安装

```powershell
1.下载安装包
进入/usr/local/目录下
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel62-3.4.3.tgz

2.解压缩
tar zxvf mongodb-linux-x86_64-rhel62-3.4.3.tgz

3.在mongodb-linux-x86_64-rhel62-3.4.3中创建data文件夹
cd mongodb-linux-x86_64-rhel62-3.4.3
mkdir data

4.在data文件夹中创建db目录和logs目录
cd data
mkdir db
mkdir logs

5.在logs目录下创建mongodb.log文件
cd logs
touch mongodb.log

6.在data目录下创建mongodb.conf文件
vi mongdb.conf
并且写入以下的内容：

#端口号
port = 27017
#数据目录
dbpath = /usr/local/mongodb/data/db
#日志目录
logpath = /usr/local/mongodb/data/logs/mongodb.log
#设置后台运行
fork = true
#日志输出方式
logappend = true
#开启认证
auth = true

export PATH=/usr/local/mongodb/bin:$PATH
通过修改profile文件:
vim /etc/profile
/export PATH //找到设置PATH的行，添加
export PATH=/usr/local/mongodb/bin:$PATH
生效方法：系统重启
有效期限：永久有效
用户局限：对所有用户
输入“source ~/.bashrc”命令，立即生效

7.启动服务器
进入到bin目录当中，使用命令
./mongod --config /usr/local/mongodb-linux-x86_64-rhel62-3.4.3/data/mongodb.conf
#启动MongoDB
 ./mongod --config /usr/local/mongodb/data/mongdb.conf 
即可按照配置文件启动服务器。

8.创建用户
注意我们之前的配置文件中auth = true开启了认证模式，所以我们需要创建认证用户才可以操作数据库。

use admin
db.createUser(
  {
    user: "yeahsir",
    pwd: "yeahsir",
    roles: [ { role: "root", db: "admin" } ]
  }
);
exit;
这样便创建了一个超级用户。
https://blog.csdn.net/xiaoxiangzi520/article/details/81094378
```



#### 上面是使用Linux正安装的步骤，跟着步骤走，问题不大，下面我们通过Docker来进行安装MongoDB和使用的方式

```powershell
1. 运行安装命令（安装MongoDB容器）：
docker run --name mongodb -p 27017:27017 -d mongo --auth

2. 进入MongoDB
docker exec -it 容器id/name mongo admin

3. 创建一个 admin 管理员账号（注意：第一句是创建一个root账号，第二个是我随便创建账号，只为了演示）
db.createUser({ user: 'root', pwd: 'root', roles: [ { role: "root", db: "admin" } ] });
db.createUser({ user: 'yeahsir', pwd: 'yeahsir', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });

4. 退出
exit

5. 以 admin 用户身份进入mongo
docker exec -it 容器id/name mongo admin

6. 对 admin 进行身份认证，就是上一步创建的管理员账号密码
#db.auth("root","root");
db.auth("yeahsir","yeahsir");
```



# 三、基本概念的理解

```powershell
知识点
    数据库（Database）
    集合（Collection）
    文档（Document）
    
关系型数据库
    数据库（Database）
    数据表（Table）
    记录（Record）
    
数据库使用步骤
    建立数据库（Whxdblog）
    建立数据集合（Posts, Categories, Tags）
    建立数据（Post:{"_id":"","title":""}）
    
Whxdblog
    Posts
        {"_id":"1","title":"我的第一篇博客"}
        {"_id":"2","title":"我的第二篇博客"}
        {"_id":"3","title":"我的第三篇博客","delflg":1}
    Categories
        {"_id":"1","title":"游戏"}
        {"_id":"2","title":"技术"}
    Tags
        {"_id":"1","title":"光荣系列"}
        {"_id":"2","title":"任天堂系列"}
        {"_id":"3","title":"Ubuntu"}
        
NoSql
	在NoSql的数据库中，操作数据都是通过指令或程序语言完成的，比如在MongoDB中使用过Javascript和JSON数据结构，来操作和管理数据的。
```

# 四、简简单单MongoDB

```powershell
登陆：db.auth("admin","123456") 

$ mongo
> help
> exit
$ mongo
> show dbs;
> use Whxdblog;
> show collections;
> db.createCollection("posts");
> db.createCollection("categories");
> db.createCollection("tags");
> show collections;
> show dbs;
> db.stats();
> db.dropDatabase();
> show dbs;
```

# 五、操作集合（Collection）

```powershell
$ mongo
> show dbs;
> use Whxdblog;
> show collections;
> db.createCollection("users");
> show collections;
> db.users.renameCollection("staff"); // users -> staff
> show collections;
> db.staff.drop();
> show collections;
> db.dropDatabase();
> show dbs;
```

# 六、操作文档（Document）

```powershell
$ mongo
> use komablog;
> show collections;
> db.createCollection("posts");
> db.posts.insert(
... {
...     title: "我的第一篇博客",
...     content: "已经开始写博客了，太激动了。"
... }
... );
> show collections;
> db.posts.find();
> db.posts.insert(
... {
...     title: "我的第二篇博客",
...     content: "写点什么好呢？",
...     tag: ["未分类"]
... }
... );
> db.posts.find();
> for(var i = 3; i <=10; i++ ) {
...     db.posts.insert({
...         title: "我的第" + i + "篇博客"
...     });
... }
> db.posts.find();
> db.posts.count();
> db.posts.remove({});
> db.posts.count();
> db.posts.find();
```

# 七、带条件的文档 db.find

```powershell
$ mongo
> use komablog;
> db.posts.remove({});
> db.posts.insert({title:"怪物猎人世界评测","rank":2,"tag":"game"});
> db.posts.insert({title:"纸片马里奥试玩体验","rank":1,"tag":"game"});
> db.posts.insert({title:"Utunbu16LTS的安装","rank":3,"tag":"it"});
> db.posts.insert({title:"信长之野望大志销量突破10000","rank":4,"tag":"game"});
> db.posts.insert({title:"Ruby的开发效率真的很高吗","rank":7,"tag":"it"});
> db.posts.insert({title:"塞尔达传说最近出了DLC","rank":4,"tag":"game"});
> db.posts.find({"tag": "game"});
#大于等于
> db.posts.find({"rank": {$gte: 4}});
#大于
> db.posts.find({"rank": {$gt: 4}});
#小于等于
> db.posts.find({"rank": {$lte: 4}});
#小于
> db.posts.find({"rank": {$lt: 4}});
#带u的
> db.posts.find({"title": /u/});
#R开头的
> db.posts.find({"title": /^R/});
#U开头的
> db.posts.find({"title": /^U/});
#类似我们的group by
> db.posts.distinct("tag");
```

# 八、复杂条件抽文档 db.find

```powershell
$ mongo
> use komablog;
> db.posts.find();
#标题带u并且排名>=5 where title like '%u%' and rank >= 5
> db.posts.find({"title": /u/, "rank":{$gte:5} });
#标题带u 或 排名>=5
> db.posts.find({$or: [{"title": /u/}, {"rank":{$gte:4}}] });
> db.posts.find({"rank": {$in: [3,4]} });
> db.posts.insert({ "title":"惊！骑士发生重大交易", "istop": true });
#存在
> db.posts.find({"istop": {$exists: true} });
```

# 九、指定抽出字段

```powershell
$ mongo
> use komablog;
> db.posts.find();
#true和这里的1是一个意思
> db.posts.find({}, {title:true, rank:1});
> db.posts.find({}, {title:true, rank:1, _id:0});
```

# 十、文档的方法 sort limit skip

```powershell
#排序
sort()
#区间
limit()
#跳过
skip()

$ mongo
> use komablog;
> db.posts.find();
#不显示ID字段，按照rank升序排序
> db.posts.find({}, {_id:0}).sort({rank:1});
#不显示ID字段，按照rank降序排序
> db.posts.find({}, {_id:0}).sort({rank:-1});
#前三条
> db.posts.find({}, {_id:0}).limit(3);
#相当于后三条
> db.posts.find({}, {_id:0}).sort({rank:-1}).limit(3);
#只取第一条findOne
> db.posts.findOne({}, {_id:0});
> db.posts.find({}, {_id:0});
#第一页
> db.posts.find({}, {_id:0}).limit(3);
#下一页
> db.posts.find({}, {_id:0}).skip(3).limit(3);
```

# 十一、文档更新 update

```powershell
update(<filter>,<update>,<options>)
命令参考网页:https://docs.mongodb.com/manual/reference/method/db.collection.update

$ mongo
> use komablog;
> db.posts.findOne({"title":"怪物猎人世界评测"});
# $set 设置 修改
> db.posts.update({"title":"怪物猎人世界评测"}, {$set: {"rank": 10} });
> db.posts.find();
# 危险操作
# update user set age = 123 where id = 1
#  where  {"title":"怪物猎人世界评测"}
# set {"rank": 99}
> db.posts.update({"title":"怪物猎人世界评测"}, {"rank": 99});
> db.posts.find();
# 只更新第一条
# update posts set rank = 50 where tag = "it" -- mysql
> db.posts.update({"tag":"it"}, {$set: {"rank": 50}});
> db.posts.find();
# multi: true 更新全部满足的
> db.posts.update({"tag":"it"}, {$set: {"rank": 60}}, {multi: true});
> db.posts.find();
```

# 十二、玩几个特殊函数 inc mul rename set unset

```powershell
$inc:递加
$mul:相乘
$rename:改名
$set:新增or修改
$unset:字段删除

$ mongo
> use komablog;
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$inc: {rank: 1}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$mul: {rank: 2}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$rename: {"rank": "score"}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$set: {"istop": true}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$unset: {"istop": true}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
```

# 十三、文档的特殊更新 upsert

```powershell
upsert:有则更新，无则追加
remove:条件删除数据

$ mongo
> use komablog;
> db.posts.find({}, {_id:0});
> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":5,"tag":"game"});
> db.posts.find({}, {_id:0});
> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":5,"tag":"game"}, {upsert:true});
> db.posts.find({}, {_id:0});
> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":7,"tag":"game"}, {upsert:true});
> db.posts.find({}, {_id:0});
> db.posts.remove({title:"其实创造比大志好玩"});
> db.posts.find({}, {_id:0});
```

# 十四、使用索引 createIndex getIndexes

```powershell
#获取所有索引
getIndexes()
#创建
createIndex({...}, {...})
#删除
dropIndex({...})

$ mongo
> use komablog;
> db.posts.getIndexes();
> db.posts.createIndex({rank:-1});
> db.posts.getIndexes();
> db.posts.dropIndex({rank:-1});
> db.posts.getIndexes();
# 建一个标题的升序索引，唯一
> db.posts.createIndex({title:1}, {unique:true});
> db.posts.getIndexes();
> db.posts.find({}, {_id:0});
> db.posts.insert({title:"怪物猎人世界评测"});
```

# 十五、备份和恢复 mongodump mongorestore

```powershell
#备份
mongodump
#恢复
mongorestore

mongodump命令脚本语法如下：
>mongodump -h dbhost -d dbname -o dbdirectory
-h：
	MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017
-d：
	需要备份的数据库实例，例如：test
-o：
	备份的数据存放位置，例如：c:\data\dump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。

$ mongo
> show dbs;
> use komablog;
> db.posts.find({}, {_id:0});
> exit
$ mkdir dbbak
$ cd dbbak
$ mongodump -d komablog
$ ls
$ mongo komablog
> db.posts.find({}, {_id:0});
> db.posts.remove({});
> db.posts.find({}, {_id:0});
> exit
$ mongorestore --drop
$ mongo komablog
> db.posts.find({}, {_id:0});
> exit
$ mongodump --help


设置完之后：下次用mongodb的时候就得：
1：启动mongodb
./mongod --auth --dbpath=/usr/local/mongodb/data/ --logpath=/usr/local/mongodb/dblogs --fork
2：用户登录：
./mongo -uroot -p123456 localhost:27017/admin

mongodump -d komablog -u=yeahsir -p=yeahsir --authenticationDatabase admin
```



# 十六、Java连接MongoDB

```
https://blog.csdn.net/qq_36760753/article/details/81149077
```

