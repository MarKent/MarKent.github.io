---
layout: post
title: 基于Mac终端SQLite3环境下的SQLite数据库操作
category: iOS
tags: SQLite
keywords: SQLite,iOS
---

### 备份(输出)数据 
#### sqilite3环境下：

- 用到的指令：

```
    sqlite> .output name.sql                 #导出SQL文件（不存在则创建）
    sqlite> .dump                            #不指定即导出整个数据库 
    sqlite> .output stdout                   #重新定向到当前数据库操作
    sqlite> .backup  '路径/自定义数据库名.db'  #直接备份已有的数据库到指定位置（可自定义备份的数据库名如custom.db）
```
    
- 在当前路径下备份数据库内所有数据
     
```
    sqlite> .output databaseBackup.sql
    sqlite> .dump    
    sqlite> .output stdout
```     

* 在当路径下备份指定的数据表
    * 比如数据库里有一份名为Students的数据表
    
```
    sqlite> .output studentsBackup.sql
    sqlite> .dump Students
    sqlite> .output stdout
```
    
通过上述方式备份后，在当前路径下会生成两个SQL文件
    
```
    databaseBackup.sql
    syudentsBackup.sql
```            
这两个文件里记录着创建数据表时的SQL语句 
    
* 导出指定表数据为CSV或html格式数据
    * 如导出名为Students的数据表
        
```
    sqlite> .output students.csv(或者.html)`
    sqlite> .mode col/.headers on              #可以设置导出的样式
    sqlite> select * from Students;     
    sqlite> .output stdout
```            
    
#### sqilite3环境外

* 用到的指令：导出为SQL文件（不存在则创建）（可以指定路径）

~~~
    >$: sqlite3 数据库名.db(or sqlite) .dump > databasebackup.sql
~~~

* 备份到当前路径下
    
```
    将要备份的数据库导出为SQL文件（不存在则创建)
    xxxx@xxx:test $: sqlite3 数据库名.db(or sqlite) .dump > backup.sql
```

* 备份到指定路径下

```    
    备份到指定路径（以备份到桌面为例)
    xxxx@aaa:test $: sqlite3 数据库名.db(or sqlite) .dump > /Users/aaa/desktop/backup.sql
```
---

### 导入数据库
#### sqlite3环境内
   
* 只有sql文件的情况下，如**`databaseBackup.sql`**
    
    * 用到的指令与步骤：
    
```
    >$: sqlite3 database.db                 #用终端新建一个数据库
    sqlite> .read databaseBackup.sql        #导入数据库的sql文件
    sqlite> 可以开始操作数据库 
```   
`或者`

```
    >$: sqlite3                             #通过终端进入sqlite3环境
    sqlite> .backup database.db             #如果数据库不存在，就备份生成一个
    sqlite> .open database.db               #打开新的数据库（已导入数据）
    sqlite> .read databaseBackup.sql        #导入数据库的sql文件操作数据库
    sqlite> 可以开始操作数据库
```
   
`或者`

```
    >$: sqlite3                             #通过终端进入sqlite3环境
    sqlite> .restore database.db            #如果数据库不存在，就恢复生成一个
    sqlite> .open database.db               #打开新的数据库
    sqlite> .read databaseBackup.sql        #导入数据库的sql文件操作数据库
    sqlite> 可以开始操作数据库
```
后两者必须要.open database.db才能实时更新操作数据库，顺序最好不要乱



- 向指定表导入cvs或html格式的数据
    
    `sqlite> .import xxx.cvs(xxx.html) 目标表名字`
    

#### sqlite3环境外

* 用到的指令：

```
    >$: sqlite3 database.db < databaseBackup.sql  #从sql文件恢复数据库
    >$: sqlite2 database.db                       #打开数据库
```




