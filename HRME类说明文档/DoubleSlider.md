### 说明

自定义控件，带有浮点型数据的滑动条，Qt自带的滑动条只能使用整型数据

### 信号

```c++
	/*
	 * @brief	当范围改变时触发
	 */
	void rangeChanged(double min, double max);

	/*
	 * @brief	点击并移动时触发
	 */
	void sliderMoved(double value);

	/*
	 * @brief	数值变化时触发
	 */
	void valueChanged(double value);
```

### 接口

```c++
	/*
	 * @brief		设置小数精确位数，多余的小数位则会省略
	 * @param[in]	digits	精确位数
	 * @note		新增接口，设置位数尽量在使用前设置，在使用过程中设置会丢失之前的位数
	 */
	void setDecimalDigits(unsigned short digits);
	unsigned short getDecimalDigits();

	/*
	 * @brief	设置/获取最大值
	 */
	void setMaximum(double maximum);
	double maximum() const;

	/*
	 * @brief	设置/获取最小值
	 */
	void setMinimum(double minimum);
	double minimum() const;

	/*
	 * @brief	设置/获取PageUp和PageDown的步数
	 */
	void setPageStep(double steps);
	double pageStep() const;

	/*
	 * @brief 设置/获取单步步数
	 */
	void setSingleStep(double single_step);
	double singleStep() const;

	/*
	 * @brief	设置/获取位置
	 */
	void setSliderPosition(double postion);
	double sliderPosition() const;

	/*
	 * @brief	获取值
	 */
	double value() const;

	/*
	 * @brief	设置范围
	 */
	void setRange(double min, double max);

	/*
	 * @brief	设置值
	 */
	void setValue(double value);
```

