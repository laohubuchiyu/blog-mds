---
title: 数据库设计(针对mysql)
---
随着cpu的计算能力越来越强，在互联网项目中，数据库的IO处理能力往往是瓶颈所在，好的数据库设计就显得格外重要。数据库设计有很多规范，但在实际项目中，我们需要根据所需，合适的‘牺牲’一些规范。

## 数据库设计原则

* 减少数据冗余

* 避免数据维护异常

* 节约存储空间

* 高效访问

## 需求分析

### 需求分析要点

1. 存储数据是什么
2. 数据存储特点（时效性数据【定期清理机制】；没有时效性的数据）
3. 数据的生命周期（增长速度快，量大，应采用拆表分表方式设计）

## 逻辑设计

* 第一范式：

 所有字段均为单一属性且不可再分。（要求为二维表）

* 第二范式(2NF):

 数据库的表中不存在非关键字段对任一候选关键字段的部分函数依赖
部分函数依赖是指存在着组合关键字中的某一关键字决定非关键字的情况。所有单关键字段的表都符合第二范式。不符合第二范式要求表，对表进行拆分。

* 第3范式（3NF）：

 若数据表不存在非关键字段，对任意候选关键字段的传递函数依赖则符合第3范式。

## 物理设计

1. 选择合适的数据库管理系统
2. 定义数据库、表以及字段的命名规范
3. 根据所选的dbms系统选择合适的字段类型
4. 反范式化设计（比如为了读效率 增加数据库的冗余）

### mysql存储引擎选择
    innodb

### 表及字段的命名规则

1. 可读性原则（使用大小写来格式化的库对象名以获得良好的可读性）
2. 表意性原则（对象的名字应该能描述出它所标识的的对象）
3. 长名原则（尽可能少使用或不适用缩写）

### 字段类型选择原则

1. 同样的数据，字符数据往往比数字处理慢
2. 在数据库中，数据处理以页为单位，数据库的瓶颈在于IO，列的长度越小，利于性能提升
3. char与varchar:固定长度适合用char（但不宜超过50个字节,大概为15个字符）
4. datetime与int：int更省空间，但datetime方便查询
5. decimal与float：decimal适合精确数据

### 其他注意事项

1. 避免使用外键约束：外键约束会带来性能问题和维护成本，但关联的列上还是要建索引
2. 避免使用触发器
3. 严禁使用预留字段
4. 反范式化：互联网项目读操作远大于写操作,用空间换时间

## 维护优化

### 维护数据字典

 导出数据字典sql

    select a.table_name, b.table_comment, a.column_name, a.column_type, a.column_comment 
    from INFORMATION_SCHEMA.COLUMNS a 
    join INFORMATION_SCHEMA.TABLES b on a.table_schema = b.table_schema and a.table_name = b.table_name 
    where a.table_name = 'customer';

### 维护索引

* 过多的索引不但会降低写的效率，也会降低多的效率
* 清理索引碎片

### 数据库中合适的操作

* 逐条操作vs批量操作
* 禁止select*

### 拆表

* 垂直拆分：为了控制表的宽度大小
* 水平拆分：分表，散表
