sequence file 存储格式

在Hadoop中存储压缩文件的一个缺点就是，通常这些文件是不可分割的。可分割的文件可以划分成多个部分，由多个mapper并行进行处理。
大多数的压缩文件是不可分割的，也就是说只能从头读到尾。
Hadoop所支持的sequence file存储格式可以将一个文件划分成多个块，然后采用一种可分割的方式对块进行压缩。

如果想在hive中使用sequence file存储格式，那么需要在CREATE TABLE语句中通过STOREED AS SEQUENCEFILE语句进行指定:
CREATE TABLE test_table STORED AS SEQUENCEFILE;

sequence file 提供了3中压缩方式: NONE、RECORD和BLOCK，默认是RECORD级别（也就是记录级别）。不过，通常来说，BLOCK级别(也就是块级别)
压缩性能最好而且是可以分割的。用户可以在Hadoop的mapred-site.xml 文件进行定义，或者在Hive的hive-site.xml 文件中进行定义，还可以在
脚本中进行指定。

RCfile
并非所有的工具和数据存储都是采用行式存储的方式的，对于特定的数据和应用来说，采用列式存储有时会更好。
例如，指定的表具有成百上千个字段，而大多数的查询只需要到其中的一小部分字段，这时，扫描所有的行而过滤掉大部分的数据显然是浪费。
然而，如果数据是按照列而不是行进行存储的话，那么只要对其需要的列的数据进行扫描就可以了，这样可以提高性能。