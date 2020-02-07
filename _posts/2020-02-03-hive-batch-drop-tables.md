---
title: "Hive: batch drop tables with pattern"
layout: post
date: 2020-02-03 15:59
guid: urn:uuid:2ac2f618-8d18-4e87-a8c1-7fc3e833c28d
author: "Lixiang"
tags:
  - Hive
---

Kylin运行一段时间后，Hive中会产生一些垃圾中间表。Kylin清理Hive临时表的脚本在3.0.0版本中才修复，针对旧版本的Kylin，可以通过脚本批量清理临时表。

```shell
#!/bin/sh

hive -e "use default; show tables;" | grep kylin_intermediate_xxx_ | while read line
do
echo -n "drop table $line;" >> droptables.lst
done
tables=`cat droptables.lst`
echo $tables
hive -e "use default; $tables"
#
rm droptables.lst
```

OR

```shell
hive -e "show tables 'kylin_intermediate_xxx_*'" | xargs -I '{}' hive -e 'drop table {}'
```

针对少量的数据，上述脚本可以执行成功。常态化的清理，可以设置定时任务，定时清理临时中间表。


因为历史原因，Hive中产生了4000+的临时表，直接运行上面的脚本会报错 `Argument list too long`。

遂改成将`DROP TABLE`语句存入`droptables.hql`，执行`hive -f droptables.hql`。

FYI:
- [Hive批量删除数据表](https://www.jianshu.com/p/18fcd972b95d)
- [Dropping multiple tables with same prefix in Hive](https://stackoverflow.com/questions/35888972/dropping-multiple-tables-with-same-prefix-in-hive)
