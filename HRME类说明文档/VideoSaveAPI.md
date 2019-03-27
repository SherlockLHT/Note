### 说明

视频保存类，使用opencv的内部接口

### 信号

```c++
	/*
	 * @brief	开启录制视频线程
	 */
	void signal_StartVideotape();
```

### 接口



```c++
	/*
	 * @brief	开始视频录制
	 * @param[in]	video_path	保存路径
	 * @param[in]	video_type	视频类型
	 * @param[in]	frame_rate	帧率
	 * @param[in]	width		图像宽
	 * @param[in]	height		图像高
	 */
	bool StartVideotape(const std::string& video_path, VideoType video_type, double frame_rate, int width, int height);

	/*
	 * @brief	关闭
	 */
	void Close();

	/*
	 * @brief 是否开始视频录制
	 */
	bool IsVideotaping() const;

	/*
	 * @brief	获取最近一次的错误信息
	 */
	std::string GetLastError() const;

	/*
	 * @brief	获取所有支持的视频格式
	 * @note	顺序按照内置的格式
	 */
	static void GetAllVideoFormat(QStringList* all_video_type);
	static void GetAllVideoFormat(QVector<QString>* all_video_type);
```

