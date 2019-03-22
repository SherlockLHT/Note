[TOC]

显示类型转换又称强制类型转换，C++的函数有四种：**static_cast**、**dynamic_cast**、**reinterpret_cast** 和 **const_cast**。

## static_cast

```c++
static_cast<type-id>(expression)
```

- 把 **expression** 转换成 **type-id** 类型
- 运行时没有类型检查来保证安全性

#### 使用场景

- void* -> 其他类型指针，但是不安全
- 其他类型指针 -> void*
- 基类和子类转换（指针或者应用均可）
  - 基类 -> 子类，下行转换，没有动态类型检查，不安全
  - 子类 -> 基类，上行转换，安全
- 替代标准转换

## dynamic_cast

```c++
dynamic_cast<type-id>(expression)
```

- 把 **expression** 转换成 **type-id** 类型
- 在上/下行转换的时候有类型检查

