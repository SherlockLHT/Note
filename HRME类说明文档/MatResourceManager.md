### 说明

opencv数据缓存模块

- 相机采集的图像转换成opencv的mat类型之后，保存在此模块中

- 算法模块从此模块中获取opencv的mat图像数据，进行数据处理
- 单例模式，线程安全

### 接口

```c++
	/*
	 * @brief	设置原始图像Mat数据
	 */
	void SetOriginMatForProcess(const cv::Mat& mat);

	/*
	 * @brief	设置作为灰度计算的原始图像Mat数据
	 */
	void SetOriginMatForGreyValueCalc(const cv::Mat& mat);

	/*
	 * @brief	设置处理完成图像的Mat数据
	 */
	void SetProcessedMat(const cv::Mat& mat);

	/*
	 * @brief	获取原始图像Mat数据
	 */
	bool GetOriginMatForProcess(cv::Mat& mat);

	/*
	 * @brief	获取作为灰度计算的原始图像的Mat
	 */
	bool GetOriginMatForGreyValueCalc(cv::Mat& mat);

	/*
	 * @brief	获取处理完成图像的Mat数据
	 */
	bool GetProcessedMat(cv::Mat& mat);
```

