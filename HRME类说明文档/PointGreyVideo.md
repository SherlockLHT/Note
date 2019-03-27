

### 说明

**此类暂停使用**

原本用于保存视频，内部使用灰点官方SDK的接口，但是后期考虑到相机可能会变，不能局限于特定相机库，保存视频的功能改用opencv的接口，此类即弃用，保存视频的类详见**VideoSaveAPI**

### 接口

```c++
	/*
	 * @brief		开始保存视频
	 * @param[in]	avi_path	视频路径
	 * @param[in]	frame_rate	帧速率
	 * @param[in]	avi_option	应用于AVI文件上的选项
	 */
	bool StartVideotape(const std::string& avi_path, FlyCapture2::AVIOption avi_option);

	/*
	 * @brief		开始保存视频
	 * @param[in]	MJPG_option		应用于MJPG文件上的选项
	 */
	bool StartVideotape(const std::string& avi_path, FlyCapture2::MJPGOption MJPG_option);

	/*
	 * @brief		开始保存视频
	 * @param[in]	H264_option		应用于H264文件上的选项
	 */
	bool StartVideotape(const std::string& avi_path, FlyCapture2::H264Option H264_option);

	/*
	 * @brief	追加图像
	 */
	bool AppendImage(const FlyCapture2::Image* image);

	/*
	 * @brief	结束录像
	 */
	bool StopVideotope();

	/*
	 * @brief	当前是否在录像
	 */
	bool IsVideotoping();

	/*
	 * @brief		设置/获取最大的视频大小
	 * @param[in]	max_size	最大大小
	 * @note		超出限制部分会创建新的视频文件。设置为0表示没有大小限制
	 */
	void SetMaximumVideoSize(unsigned int max_size);
	unsigned int GetMaximumVideoSize();

	/*
	 * @brief	获取最近一次的错误描述
	 */
	std::string GetLastErrorDescription();
```

