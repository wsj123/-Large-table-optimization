## MySQL大表优化方案

除非单表数据未来会一直不断上涨，否则不要一开始就考虑拆分，拆分会带来逻辑、部署、运维的各种复杂度，一般以整型值为主的表在千万级以下，字符串为主的表在五百万以下是没有太大问题的。而事实上很多时候MySQL单表的性能依然有不少优化空间，甚至能正常支撑千万级以上的数据量：

### 字段
尽量使用TINYINT、SMALLINT、MEDIUM_INT作为整数类型而非INT，如果非负则加上UNSIGNED

VARCHAR的长度只分配真正需要的空间

使用枚举或整数代替字符串类型

尽量使用TIMESTAMP而非DATETIME，

单表不要有太多字段，建议在20以内

避免使用NULL字段，很难查询优化且占用额外索引空间

用整型来存IP

### 索引

索引并不是越多越好，要根据查询有针对性的创建，考虑在WHERE和ORDER BY命令上涉及的列建立索引，可根据EXPLAIN来查看是否用了索引还是全表扫描

应尽量避免在WHERE子句中对字段进行NULL值判断，否则将导致引擎放弃使用索引而进行全表扫描

值分布很稀少的字段不适合建索引，例如"性别"这种只有两三个值的字段

字符字段只建前缀索引

字符字段最好不要做主键

不用外键，由程序保证约束

尽量不用UNIQUE，由程序保证约束

使用多列索引时主意顺序和查询条件保持一致，同时删除不必要的单列索引

### 查询SQL
```
[通过开启慢查询日志来找出较慢的SQL](https://www.cnblogs.com/luyucheng/p/6265594.html)
```

不做列运算：SELECT id WHERE age + 1 = 10，任何对列的操作都将导致表扫描，它包括数据库教程函数、计算表达式等等，查询时要尽可能将操作移至等号右边

sql语句尽可能简单：一条sql只能在一个cpu运算；大语句拆小语句，减少锁时间；一条大sql可以堵死整个库

不用SELECT *

OR改写成IN：OR的效率是n级别，IN的效率是log(n)级别，in的个数建议控制在200以内

不用函数和触发器，在应用程序实现

避免%xxx式查询

少用JOIN

使用同类型进行比较，比如用'123'和'123'比，123和123比

尽量避免在WHERE子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描

对于连续数值，使用BETWEEN不用IN：SELECT id FROM t WHERE num BETWEEN 1 AND 5

列表数据不要拿全表，要使用LIMIT来分页，每页数量也不要太大

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```
