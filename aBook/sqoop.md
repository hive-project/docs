sqoop作为业务数据库同步的一种方式。

作业执行MR流程。

文件格式不支持列式存储。

## 两种执行方式

### 传统方式
```
sqoop export --connect "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8" --username root --password 123456 --table person -m 1 --export-dir person_export --input-fields-terminated-by ' ' --columns="id,name"
```

input-fields-terminated-by可以用来指定hdfs上文件的分隔符，默认是逗号


### Hcatalog
```
sqoop export \
  --hcatalog-database temp \
  --hcatalog-table js_pianyuan_orc \
  --hcatalog-partition-keys curdate \
  --hcatalog-partition-values 20180122 \
  --update-mode allowinsert \
  --connect jdbc:mysql://ip:3306/test \
  --username username \
  --password passwd --m 10\
  --table js_pianyuan
```


1.更新导出（updateonly模式）
– update-key，更新标识，即根据某个字段进行更新，例如id，可以指定多个更新标识的字段，多个字段之间用逗号分隔。

– updatemode，指定updateonly（默认模式），仅仅更新已存在的数据记录，不会插入新纪录。

2.更新导出（allowinsert模式）
– update-key，更新标识，即根据某个字段进行更新，例如id，可以指定多个更新标识的字段，多个字段之间用逗号分隔。

– updatemod，指定allowinsert，更新已存在的数据记录，同时插入新纪录。实质上是一个insert & update的操作
