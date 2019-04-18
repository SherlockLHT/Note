## 说明

**ORDER BY** 用于对结果集进行排序，默认升序，和 **DESC** 结合使用可以倒序

## 示例

假设由数据如下：

|  id  | name | price |
| :--: | :--: | :---: |
|  1   |  aa  |  111  |
|  2   |  bb  |  222  |
|  3   |  aa  |  333  |
|  4   |  cc  |  111  |

```sql
SELECT * FROM table ORDER BY name;
```

按照 **name** 排序，结果为： 1、3、2、4（id）

```sql
SELECT* FROM table ORDER BY price DESC;
```

按照 **price** 逆序，结果为：3、2、1、4（id）