LogQL (Log Query Language) is the query language used by the Loki logging system, designed inspired by PromQL (Prometheus Query Language). It is mainly used to query and analyze log data in Loki. LogQL queries are mainly divided into two parts:

**Log Stream Selector**

Used to determine the log stream to query. The syntax of this part is similar to PromQL, mainly identifying the log stream through the log's labels.

It consists of one or more key-value pairs, where each key is a log label, and the value is the value of that label. These key-value pairs are enclosed in a pair of curly braces {}.

Basic syntax: `{label="value", label2="value2", ...}`

Matching operations:

- = : Exact match
- != : Not match
- =~ : Regular expression match
- !~ : Regular expression not match

**Filter Expression**

The filter expression is used to further filter the content of log lines, narrowing down the range of query results. It can be used after the log stream selector and can include multiple filter conditions that can be chained for multiple filtering.

Matching operations:

- |= : Log line contains string
- != : Log line does not contain string
- |~ : Log line matches regular expression
- !~ : Log line does not match regular expression

Examples:

```shell
# Query task logs of the management platform containing ERROR keyword
{yasom="omlogs",filename="/data/ycp/ycm/log/task.log"}|="ERROR"

# Query logs of the managed server containing ERROR keyword
{host="host-67e6702d"}|="ERROR" # host-67e6702d is the host ID

# Query database logs containing ERROR keyword
{SalesDB_669b0fea=~"1-1|1-2"}|="ERROR"  # where SalesDB is the database name, 669b0fea is the database identifier, and 1-1 is the database node ID
```
