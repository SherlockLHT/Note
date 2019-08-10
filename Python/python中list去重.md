参考：https://blog.csdn.net/BabyFish13/article/details/79290434

## 去重，顺便变乱

```python
l1 = ['b','c','d','b','c','a','a']
l2 = list(set(l1))
print l2
```

```python
l1 = ['b','c','d','b','c','a','a']
l2 = {}.fromkeys(l1).keys()
print l2
```

## 保持顺序不变

```python
l1 = ['b','c','d','b','c','a','a']
l2 = list(set(l1))
l2.sort(key=l1.index)
print l2
```

```python
l1 = ['b','c','d','b','c','a','a']
l2 = sorted(set(l1),key=l1.index)
print l2
```

```python
l1 = ['b','c','d','b','c','a','a']
l2 = []
for i in l1:
    if not i in l2:
        l2.append(i)
print l2
```

```python
l1 = ['b','c','d','b','c','a','a']
l2 = []
[l2.append(i) for i in l1 if not i in l2]
print l2
```

