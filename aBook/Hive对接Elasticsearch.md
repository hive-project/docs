## ES-Hadoop方案
下载地址： https://www.elastic.co/cn/what-is/elasticsearch-hadoop  

优势： 方式比较成熟，针对固定字段，操作比较简单，先建立hive映射表，从映射表中读取数据写入到数据仓库ODS层。

* 使用demo：
```
ADD JAR hdfs:///user/hive/jars/elasticsearch-hadoop-hive-7.9.3.jar;
ADD JAR hdfs:///user/hive/jars/commons-httpclient-3.1.jar;
CREATE EXTERNAL TABLE if not exists default.es_bigdata_test (
 `appid` string,
 `baseInfo` STRUCT<asn:bigint, clientVersion:string,configVersion:string,deviceKey:string,deviceType:bigint,dnsVersion:string,ip:string,ipIsp:string,ipPrefix:string,ipRegion:string,language:string,model:string,netType:string,operatorCode:string,operatorName:string,osVersion:string,region:string,uid:string,uidRegion:string>,
 `beat` STRUCT<hostname:string,name:string,version:string>,
 `btype` string,
 `count` bigint,
 `duration` bigint,
 `from` string,
 `loading` string,
 `logTime` string,
 `mpVersion` string,
 `mp_host` string,
 `mp_proto` string,
 `mp_uri_param` string,
 `mp_uri_path` string,
 `name` string,
 `offset` bigint,
 `pageTitle` string,
 `pageUrl` string,
 `param` STRUCT<appid:string,campaignId:string>,
 `port` string,
 `prospector` STRUCT<type:string>,
 `source` string,
 `time` timestamp
)
STORED BY 'org.elasticsearch.hadoop.hive.EsStorageHandler'
TBLPROPERTIES('es.resource' = 'logs-footprint-mp-2020.11.24/doc',
'es.index.auto.create' = 'true', --自动建index
'es.index.read.missing.as.empty'='true',
'es.nodes' = 'zhenxin.com',
'es.port'='9200'
);
```

* 读取数据方式
```
ADD JAR hdfs:///user/hive/jars/elasticsearch-hadoop-hive-7.9.3.jar;
ADD JAR hdfs:///user/hive/jars/commons-httpclient-3.1.jar;
select
 `appid`
 ,`baseInfo`
 ,`beat`
 ,`btype`
 ,`count`
 ,`duration`
 ,`from`
 ,`loading`
 ,`logTime`
 ,`mpVersion`
 ,`mp_host`
 ,`mp_proto`
 ,`mp_uri_param`
 ,`mp_uri_path`
 ,`name`
 ,`offset`
 ,`pageTitle`
 ,`pageUrl`
 ,`param`
 ,`port`
 ,`prospector`
 ,`source`
 ,`time`
from default.es_bigdata_test limit 100;
```

## elasticdump方案
该方法的作用是将es的数据下载到本地文件，然后将文件上传到集群特定目录，再通过修复hive数据表分区完成。

```
elasticdump \
  --input=http://xxxxx:9200/logs-2020.11.24 \
  --output=/data/my_index.json \
  --type=data
  --limit=10000
 
 
elasticdump \
  --input=http://xxxxx:9200/logs-2020.11.24 \
  --output=/data/my_index_mapping.json \
  --type=mapping
```
