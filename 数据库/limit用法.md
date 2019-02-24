[TOC]

## 说明

限制查询结果条数

## 格式

```sql
select * from table_ limit start, offset;
```

- 从 **index** 为 **start** 开始查询，偏移，即数量是 **offset**；
- **index** 从 **0** 开始计算；

**特殊情况：**

- 若 **offset** 是 **-1**，则为之后所有；
- 若无 **start**，则默认为 **0**；

## 示例

```sql
select * from table_ limit 2, 3;
```

检索记录行为 **3 ~ 6** 行。

```sql
select * from table_ limit 2, -1;
```

检索记录行为 **3 ~ 最后** 行。

```sql
select * from table_ limit 3;
//等同于select from * table_ limit 0, 3;
```

检索记录行为 **1 ~ 3** 行。