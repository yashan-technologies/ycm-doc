LogQL（Log Query Language）是Loki日志系统使用的查询语言，受PromQL（Prometheus Query Language）的启发而设计。它主要用于在Loki中查询和分析日志数据。LogQL查询语句主要分为两个部分：

**日志流选择器（Log Stream Selector）**

用于确定要查询的日志流。这部分的语法与PromQL类似，主要通过日志的label来确定查询的日志流。

它由一个或多个键值对组成，每个键是一个日志标签（label），而值是该标签的值。这些键值对被包裹在一对花括号{}中。

基本语法：`{label="value", label2="value2", ...}`

匹配运算：

- =：完全匹配
- !=：不匹配
- =~：正则表达式匹配
- !~：正则表达式不匹配

**过滤器表达式（Filter Expression）**

过滤器表达式用于进一步过滤日志行内容，缩小查询结果的范围。它可以在日志流选择器之后使用，并可以包含多个过滤条件，这些条件可以串联起来进行多次过滤。

匹配运算：

- |=：日志行包含字符串
- !=：日志行不包含字符串
- |~：日志行匹配正则表达式
- !~：日志行不匹配正则表达式

示例：

```shell
# 查询管理平台的task日志且包含ERROR关键字
{yasom="omlogs",filename="/data/ycp/ycm/log/task.log"}|="ERROR"

# 查询被托管服务器日志且包含ERROR关键字
{host="host-67e6702d"}|="ERROR" # host-67e6702d 为主机ID

# 查询数据库日志且包含ERROR关键字
{SalesDB_669b0fea=~"1-1|1-2"}|="ERROR"  # 其中SalesDB为数据库名称，669b0fea为数据库标识，1-1为数据库节点ID
```
