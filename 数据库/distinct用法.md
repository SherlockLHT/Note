### 说明

- **DISTINCT** 用于剔除重复数据；
- 必须放在字段开头；

### 示例

假设有数据如下：

| id   | name | price |
| ---- | ---- | ----- |
| 1    | aa   | 11    |
| 2    | bb   | 22    |
| 3    | aa   | 33    |
| 4    | cc   | 11    |
| 6    | aa   | 11    |

#### 剔除单个重复的重复字段

```sql
select distinct name from table_name;
```

根据 **name** 字段检索，去除重复，最后得到aa、bb、cc三项；

### 剔除多个字段的重复数据

```sql
select distinct name, price from table_name;
```

根据 **name** 和 **price** 去除重复，即两个字段都相同则会被剔除，最后得到数据如下：
| name | price |
| ---- | ----- |
| aa   | 11    |
| bb   | 22    |
| aa   | 33    |
| cc   | 11    |

#### 用于统计数量

```sql
SELECT COUNT(name) FROM table_1 WHERE condition;
```
获取检索的数量，这里没有条件的话，得到5
```sql
SELECT COUNT(DISTINCT name) FROM table_1 WHERE condition;
```

在最后结果中根据 **name** 字段去重后，获取数量，这里没有条件的话，得到3