### 搜索语法

### 使用 lucene 语法搜索
##### 条件编写规范
| 名称 | 语义 |
| - | - |
| * | 查询所有内容 |
| AND | query1 AND query2，查询交集 |
| OR | query1 OR query2，查询并集 |
| NOT | query1 AND NOT query2，表示符合query1，不符合query2的结果 |
| () | 把一个或多个query合并成一个query，提升优先级 |
| [] | 区间查询，包括边界，如 `status:[400 TO 500]` 查询 `status` 字段在 400 到 500 区间内的数据 |
| {} | 区间查询，不包括边界，如 `status:{400 TO 500}` 查询 `status` 字段在 400 到 500 区间内的数据，并排除边界值 |
| \ | 转义字符，如正则表达式搜索时表达式包含反斜杠的情况，如 `request_path:/\/query\/abc\/.*/` 查询 `request_path` 字段匹配正则表达式 `/path/abc/.* `的数据 |
| >,=,<,<=,>= | 区间查询，如 `status:>=500` 查询 `status` 大于 500 的数据 |

##### 查询举例
字段名称`name`，类型`string`，包含内容`a`的记录：
>``
name:a
``

字段名称`ip`，类型`string`，包含内容`a`或`b`的记录：
>``
ip:a OR ip:b
``

字段名称`hosts`，类型`string`，包含内容`a`或者`b`,不包含`c`的记录：
>``
(hosts:a OR hosts:b) AND (NOT hosts:c)
``

字段名称`ip`，类型`string`，包含内容`a`或`b`，同时字段名称`hosts`，类型`string`，包含内容`c`的记录：
>``
(ip:a OR ip:b) AND (hosts:c)
``

字段名称`createTime`，类型`date`，包含内容`2018-10-01`到`2018-10-02`的记录：
>``
createTime:[2018-10-01 TO 2018-10-02]
``

字段名称`count`，类型`long`或者`float`，内容大于`5`的记录：
>``
count:>5
``









