假设某数据库表 ***product\_*** 的描述如下：

| Field | Type | Null | Key | Default | Extra |
| ---- | ---- |---- | ---- | ---- | ---- |
| id | int(11) | NO |PRI|NULL|auto_increment|
| name | varchar(32) | YES ||NULL||
| price | float | YES ||NULL||
| cid | int(11) | YES ||NULL||

***select*** 具体到某列：

```sql
select * from product_;
select p.id, p.name, p.price, p.cid from product_ p;
```
- 两句效果相同，搜索数据库所有列；
- 可以通过调节属性的顺序，从而调节返回结果的列顺序；

```sql
select p.id, p.name from product_ p;
```

- 只搜索 ***id*** 和 ***name*** 两列；

**注：具体到指定列，效率会高一些**