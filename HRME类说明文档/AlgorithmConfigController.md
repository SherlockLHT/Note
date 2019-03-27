### 说明

算法参数配置控制器，使用MVC模式，因为比较简单，所以这里将MV合成一个类，和AlgorithmConfigWidget一起，做为MVC模式存在，且此类为单例模式。执行算法接口时，从此类中获取算法的参数，保证算法的参数是实时的

### 接口

```c++
	/*
	 * @brief	回归对比度算法参数到默认值
	 */
	void ReturnConrastAlgorithmParam2DefaultValue();

	/*
	 * @brief	设置/获取是否需要使用对比度增强算法
	 */
	void SetContrastEnhancementAlgorithmEnabled(bool is_enabled);
	bool GetContrastEnhancementAlgorithmEnabled();

	/*
	 * @brief	设置/获取对比度增强算法是否启用CUDA加速
	 */
	void SetContrastEnhancementAlgorithmCUDAEnabled(bool is_enabled);
	bool GetContrastEnhancementAlgorithmCUDAEnabled();

	/*
	 * @brief	设置/获取梯度分割算法是否开启
	 */
	void SetGradientSegementEnabled(bool is_enabled);
	bool GetGradientSegmentEnabled();

	/*
	  * @brief	设置/获取梯度分割算法是否开启CUDA加速
	*/
	void SetGradientSegementCUDAEnabled(bool is_enabled);
	bool GetGradientSegmentCUDAEnabled();

	/*
	 * @brief	设置/获取是否使用灰度值算法
	 */
	void SetGreyAlgorithmEnabled(bool is_enabled);
	bool GetGreyAlgorithmEnabled();

	/*
	 * @brief	设置/获取灰度值是否使用矩形取样
	 */
	void SetGreyRectangleSaplingEnabled(bool is_enabled);
	bool GetGreyRectangleSaplingEnabled();

	/*
	 * @brief	设置/获取灰度值矩形取样的size
	 */
	void SetGreyRectangleSamplingSize(const QSize& size);
	QSize GetGreyRectangleSamplingSize();

	/*
	 * @brief	设置/获取圆形取样的半径
	 */
	void SetGreyCircularSamplingRadius(unsigned int radius);
	unsigned int GetGreyCircularSamplingRadius();

	/*
	 * @brief	设置/获取灰度值的每块线程行数
	 */
	void SetGreyThreadRowsPerBlock(unsigned int rows);
	unsigned int GetGreyThreadRowsPerBlock();

	/*
	 * @brief	设置/获取灰度值的每块线程列数
	 */
	void SetGreyThreadColumnsPerBlock(unsigned int columns);
	unsigned int GetGreyThreadColumnsPerBlock();

	/*
	 * @brief	设置/获取处理前对比度
	 */
	void SetAlpheBefore(int value);
	int GetAlpheBefore();

	/*
	 * @brief	设置/获取处理后对比度
	 */
	void SetAlpheAfter(int value);
	int GetAlpheAfter();

	/*
	 * @brief	设置/获取自适应窗口大小
	 */
	void SetBlockSize(int value);
	int GetBlockSize();

	/*
	* @brief	设置/获取高斯窗口大小
	*/
	void SetGuassSize(int value);
	int GetGuassSize();

	/*
	 * @brief	设置/获取中值大小
	 */
	void SetMedianSize(int value);
	int GetMedianSize();

	/*
	 * @brief	设置/获取半径
	 */
	void SetRadiusSize(int value);
	int GetRadiusSize();

	/*
	 * @brief	设置/获取处理前对比度增强系数
	 */
	void SetContrastIntensityBefore(int value);
	int GetContrastIntensityBefore();

	/*
	* @brief	设置/获取处理器前分块区域大小
	*/
	void SetBlockAreaBefore(int value);
	int GetBlockAreaBefore();

	/*
	 * @brief	设置/获取补偿系数
	 */
	void SetCompensationCoefficient(float value);
	float GetCompensationCoefficient();

	/*
	 * @brief	设置/获取处理后对比度增强系数
	 */
	void SetContrastIntensityAfter(int value);
	int GetContrastIntensityAfter();

	/*
	 * @brief	设置/获取处理后分块区域
	 */
	void SetBlockAreaAfter(int value);
	int GetBlockAreaAfter();

	/*
	 * @brief	设置/获取对比度算法的每块线程行数
	 */
	void SetContrastThreadRowsPerBlock(unsigned int rows);
	unsigned int GetContrastThreadRowsPerBlock();

	/*
	 * @brief	设置/获取对比度算法的每块线程列数
	 */
	void SetContrastThreadColumnsPerBlock(unsigned int columns);
	unsigned int GetContrastThreadColumnsPerBlock();

	/*
	 * @brief	设置/获取高低梯度分割阈值
	 */
	void SetLowHighGradientSegmentationThreshold(float threshold);
	float GetLowHighGradientSegmentationThreshold();

	/*
	 * @brief	设置梯度分割算法参数为默认值
	 */
	void SetGradientSegmentationParam2Default();

	/*
	 * @brief	获取/设置低/高梯分割算法参数
	 * @note	设置为单独设置
	 */
	void GetLowGradientSegmentationParam(IMAGE_ALGORITHM::GradientSegmentationParam* param);
	void GetHighGradientSegmentationParam(IMAGE_ALGORITHM::GradientSegmentationParam* param);
	void SetOutsideNoiseFilter(int value, bool is_low_gradient);
	void SetGammaNormalLarge(int value, bool is_low_gradient);
	void SetGammaCompensation(float value, bool is_low_gradient);
	void SetGammaValue(int value, bool is_low_gradient);
	void SetOriginBackgroundBrightCompensation(float value, bool is_low_gradient);
	void SetOriginBackgroundProportion(float value, bool is_low_gradient);
	void SetBackgroundBright(float value, bool is_low_gradient);
	void SetNucleusBright(float value, bool is_low_gradient);
	void SetCLAHEValue(int value, bool is_low_gradient);
	void SetCLAHEBlockSize(int value, bool is_low_gradient);
	void SetFilterWindow(int value, bool is_low_gradient);
	void SetRadius(int value, bool is_low_gradient);
	void SetAlpha(float value, bool is_low_gradient);

	/*
	 * @brief		获取大肠隐窝算法参数
	 * @param[out]	param	输入算法参数
	 */
	void GetGastrointestinalFossaeParam0To35(IMAGE_ALGORITHM::AlgorithmParameter* param);
	void GetGastrointestinalFossaeParam35To65(IMAGE_ALGORITHM::AlgorithmParameter* param);
	void GetGastrointestinalFossaeParam65To255(IMAGE_ALGORITHM::AlgorithmParameter* param);

	/*
	 * @brief	设置0~35范围内的大肠隐窝算法参数
	 */
	void SetGastrointestinalFossaeGaussFilterSize0To35(int value);
	void SetGastrointestinalFossaeClaheIntensity0To35(int value);
	void SetGastrointestinalFossaeClaheSize0To35(int value);
	void SetGastrointestinalFossaeGammaIntensity0To35(float value);
	void SetGastrointestinalFossaeGammaMaxBrightness0To35(int value);
	void SetGastrointestinalFossaeResultMaxBrightness0To35(int value);
	/*
	 * @brief	设置35~65范围内的大肠隐窝算法参数
	 */
	void SetGastrointestinalFossaeGaussFilterSize35To65(int value);
	void SetGastrointestinalFossaeClaheIntensity35To65(int value);
	void SetGastrointestinalFossaeClaheSize35To65(int value);
	void SetGastrointestinalFossaeGammaIntensity35To65(float value);
	void SetGastrointestinalFossaeGammaMaxBrightness35To65(int value);
	void SetGastrointestinalFossaeResultMaxBrightness35To65(int value);
	/*
	 * @brief	设置65~255范围内的大肠隐窝算法参数
	 */
	void SetGastrointestinalFossaeGaussFilterSize65To255(int value);
	void SetGastrointestinalFossaeClaheIntensity65To255(int value);
	void SetGastrointestinalFossaeClaheSize65To255(int value);
	void SetGastrointestinalFossaeGammaIntensity65To255(float value);
	void SetGastrointestinalFossaeGammaMaxBrightness65To255(int value);
	void SetGastrointestinalFossaeResultMaxBrightness65To255(int value);

	/*
	* @brief		获取鳞状细胞算法参数
	* @param[out]	param	输入算法参数
	*/
	void GetSquamousCellExtractioParam0To35(IMAGE_ALGORITHM::AlgorithmParameter* param);
	void GetSquamousCellExtractioParam35To65(IMAGE_ALGORITHM::AlgorithmParameter* param);
	void GetSquamousCellExtractioParam65To255(IMAGE_ALGORITHM::AlgorithmParameter* param);

	/*
	* @brief	设置0~35范围内的鳞状细胞算法参数
	*/
	void SetSquamousCellExtractioGaussFilterSize0To35(int value);
	void SetSquamousCellExtractioClaheIntensity0To35(int value);
	void SetSquamousCellExtractioClaheSize0To35(int value);
	void SetSquamousCellExtractioGammaIntensity0To35(float value);
	void SetSquamousCellExtractioGammaMaxBrightness0To35(int value);
	void SetSquamousCellExtractioResultMaxBrightness0To35(int value);
	/*
	* @brief	设置35~65范围内的鳞状细胞算法参数
	*/
	void SetSquamousCellExtractioGaussFilterSize35To65(int value);
	void SetSquamousCellExtractioClaheIntensity35To65(int value);
	void SetSquamousCellExtractioClaheSize35To65(int value);
	void SetSquamousCellExtractioGammaIntensity35To65(float value);
	void SetSquamousCellExtractioGammaMaxBrightness35To65(int value);
	void SetSquamousCellExtractioResultMaxBrightness35To65(int value);
	/*
	* @brief	设置65~255范围内的鳞状细胞算法参数
	*/
	void SetSquamousCellExtractioGaussFilterSize65To255(int value);
	void SetSquamousCellExtractioClaheIntensity65To255(int value);
	void SetSquamousCellExtractioClaheSize65To255(int value);
	void SetSquamousCellExtractioGammaIntensity65To255(float value);
	void SetSquamousCellExtractioGammaMaxBrightness65To255(int value);
	void SetSquamousCellExtractioResultMaxBrightness65To255(int value);

	/*
	 * @brief	设置/获取标尺文字
	 */
	void SetScaleText(const QString& text);
	QString GetScaleText();
```

