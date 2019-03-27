### 说明

作为AlgorithmConfigWidget窗口的内部窗口，为新版算法提供参数支持，新算法的参数若不单独提取出类，则会造成AlgorithmConfigWidget类的臃肿，所以将新版算法的界面部分单体提取出来，方便修改

### 信号

```c++
	/*
	 * @brief	高斯滤波大小值改变
	 */
	void SignalGaussFilterSizeValueChanged(int value);

	/*
	 * @brief	CLAHE强度值改变
	 */
	void SignalClaheIntensityValueChanged(int value);

	/*
	 * @brief	CLAHE大小改变
	 */
	void SignalClaheSizeValueChanged(int value);

	/*
	 * @brief	gamma强度值改变
	 */
	void SignalGammaIntensityValueChanged(double value);

	/*
	 * @brief	gamma最大亮度值改变
	 */
	void SignalGammaMaxBrightnessValueChanged(int value);

	/*
	 * @brief 结果最大亮度值改变
	 */
	void SignalResultMaxBrightnessValueChanged(int value);
```

### 接口

```c++
	/*
	 * @brief	初始化整体设置参数值
	 */
	void InitlizeParamValue(IMAGE_ALGORITHM::AlgorithmParameter param);

	/*
	 * @brief	设置高斯滤波窗口范围
	 */
	void SetGaussFilterSizeRange(int lower_limit, int upper_limit);
	void SetGaussFilterSizeValue(int value);

	/*
	 * @brief	设置CLAHE强度范围
	 */
	void SetClaheIntensityRange(int lower_limit, int upper_limit);
	void SetClaheIntensityValue(int value);

	/*
	 * @brief	设置CLAHE大小范围
	 */
	void SetClaheSizeRange(int lower_limit, int upper_limit);
	void SetClaheSizeValue(int value);

	/*
	 * @brief	设置gamma强度范围
	 */
	void SetGammaIntensityRange(double lower_limit, double upper_limit);
	void SetGammaIntensityValue(double value);

	/*
	 * @brief	设置gamma最大亮度范围
	 */
	void SetGammaMaxBrightnessRange(int lower_limit, int upper_limit);
	void SetGammaMaxBrightnessValue(int value);

	/*
	 * @brief	设置结果最大亮度范围
	 */
	void SetResultMaxBrightnessRange(int lower_limit, int upper_limit);
	void SetResultMaxBrightnessValue(int value);
```

