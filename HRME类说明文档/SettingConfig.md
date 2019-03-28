### 说明

ini文件读写的最顶层操作，使用Qt的方法

### 接口

```c++
	/*
	* @brief		设置INI配置
	* @param[in]	section	section
	* @param[in]	key		key
	* @param[in]	value	值
	*/
	void SetIniValue(const QString& section, const QString& key, const QVariant& value) const;

	/*
	* @brief		获取INI配置
	* @param[in]	section	section
	* @param[in]	key		key
	* @return		得到的值
	*/
	QVariant GetIniValue(const QString& section, const QString& key, const QVariant& default_value = QVariant()) const;
```

