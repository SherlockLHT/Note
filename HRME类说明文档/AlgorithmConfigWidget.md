### 说明

算法配置窗口窗口，作为MVC的V层，和AlgorithmConfigController一起作为MVC模式存在

### 信号

```c++
	/*
	 * @brief	对比度算法使能状态改变
	 */
	void signal_ContrastEnhancementAlgorithmEnabledChanged(bool is_enabled);

	/*
	 * @brief	梯度分割算法使能状态改变
	 */
	void signal_GradientSegmentAlgorithmEnabledChanged(bool is_enabled);
```

### 接口

```c++
	/*
	 * @brief	设置图像的分辨率
	 * @note	用于取样范围的设定
	 */
	void SetImageResolvtion(int width, int height);

	/*
	 * @brief	标尺文字改变
	 */
	void slot_ScaleTextChanged(const QString& text);

	/*
	* @brief	前对比度数值变化
	*/
	void slot_AlpheBeforeChanged(int value);

	/*
	* @brief	后对比度数值变化
	*/
	void slot_AlpheAfterChanged(int value);

	/*
	* @brief	自适应窗口大小值变化
	*/
	void slot_BlockSizeChanged(int value);

	/*
	* @brief		灰度算法使用状态改变
	* @param[in]	state	当前状态
	*/
	void slot_GreyAlgorithmCheckStatusChanged(int state);

	/*
	* @brief		对比度增强算法使用状态改改变
	* @param[in]	state	当前状态
	*/
	void slot_ContrastEnhancementChecStateChanged(int state);
	void slot_ContrastEnhancementChecStateChanged(bool state);

	/*
	* @brief		对比度增强算法CUDA加速使用状态改改变
	* @param[in]	state	当前状态
	*/
	void slot_ContrastEnhancementCUDAChecStateChanged(int state);

	/*
	 * @brief	高斯窗口大小改变
	 */
	void slot_GuassSizeChanged(int value);

	/*
	 * @brief	中值大小改变
	 */
	void slot_MedianSizeChanged(int value);

	/*
	 * @brief	半径变化
	 */
	void slot_RadiusChanged(int value);

	/*
	 * @brief	处理前对比度系数改变
	 */
	void slot_ContrastIntensityBeforeChanged(int value);

	/*
	 * @brief	处理后对比度系数改变
	 */
	void slot_ContrastIntensityAfterChanged(int value);

	/*
	 * @brief	处理前分块区域改变
	 */
	void slot_BlockAreaBeforeChanged(int value);

	/*
	 * @brief	处理后分块区域改变
	 */
	void slot_BlockAreaAfterChanged(int value);

	/*
	 * @brief	补偿系数改变
	 */
	void slot_CompensationCoefficientChanged(double value);

	/*
	 * @brief	点击默认按钮
	 */
	void slot_OnClickDefaultButton();

	/*
	 * @brief	灰度值取样方式改变
	 */
	void slot_GreyValueSamplingTypeChanged();

	/*
	 * @brief	灰度值的矩形取样的宽度改变
	 */
	void slot_GreyRectangleSamplingWidthChanged(int width);

	/*
	 * @brief	灰度值的矩形取样的高度改变
	 */
	void slot_GreyRectangleSamplingHeightChanged(int height);

	/*
	 * @brief	灰度值的圆形取样的半径改变
	 */
	void slot_GreyCircularSamplingRadiusChanged(int radius);

	/*
	 * @brief	梯度分割算法使能状态改变
	 */
	void slot_GradientSegmentEnabledChanged(int state);
	void slot_GradientSegmentEnabledChanged(bool state);

	/*
	* @brief	梯度分割算法CUDA加速使能状态改变
	*/
	void slot_GradientSegmentCUDAEnabledChanged(int state);
```

