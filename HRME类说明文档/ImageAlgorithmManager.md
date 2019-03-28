### 说明

图像算法模块

- 模块内创建两个线程分别进图像行算法处理和灰度值的计算；
- 模块会在初始化的时候动态加载dll，然后获取dll内算法实例的指针；

### 信号

```c++
	/*
	* @brief	当处理完一帧图像时，触发该信号
	* @param[in]	origin_frame	原始图像
	* @param[in]	frame			处理之后的图像
	*/
	void signal_FrameProcessed(const QImage& origin_frame, const QImage& frame);
	void signal_FrameProcessed(const QImage& frame);

	/*
	 * @brief	发送实时帧率
	 */
	void signal_FrameRate(double frame_rate);

	/*
	 * @brief		发送一帧处理时间
	 * @param[in]	millseconds		一帧图像处理的耗时(ms)
	 */
	void signal_OneFrameProcessTime(int millseconds);

	/*
	* @brief		灰度值计算完成之后触发该信号
	* @param[in]	grey_value	计算出的灰度值
	*/
	void signal_SendGreyValue(double grey_value);
```



### 接口

```c++
	/*
	 * @brief	开始处理
	 */
	bool StartProcess();

	/*
	 * @brief		结束处理
	 * @param[in]	time_out	等待超时，毫秒计算
	 */
	void EndProcess(int time_out = 3000);

	/*
	 * @brief	获取算法版本
	 */
	QString GetAlgorithmVersion() const;

	/*
	 * @brief	算法是否能够使用
	 */
	bool IsAlgorithmPass() const;

	/*
	 * @brief		获取最近一次处理完成的图像
	 * @param[out]	last_image 最后一次处理完图像的深拷贝
	 */
	void GetLastProcessedImage(cv::Mat& last_image);

	/*
	 * @brief	获取最近一次的错误信息
	 */
	QString GetLastErrorString() const;
```

