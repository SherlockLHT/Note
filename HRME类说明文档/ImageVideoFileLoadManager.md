### 说明

**此类暂停使用**

图片和视频加载模块，此类已经暂停使用，原本作为算法调试方便，开放加载图片和视频，并在界面显示，算法调参可以看到直挂效果，后期无此必要，关闭界面接口，不再使用

### 信号

```c++
	/*
	* @brief		读取视频时触发
	* @param[in]	total_frame		总帧数
	* @param[in]	current_frame	当前帧数
	*/
	void signal_VideoFrameCount(int total_frame, int current_frame);
```

### 接口

```c++
	/*
	* @brief	加载图片线程
	*/
	bool LoadPictureFileThread(const std::string& file_name);

	/*
	* @brief	加载视频线程
	*/
	bool LoadVideoFileThread(const std::string& file_name);

	/*
	* @brief		停止文件加载线程
	* param[in]	time_out	等待超时，以毫秒计算，默认3秒
	*/
	void EndLoadFileThread(int time_out = 3000);

	/*
	* @brief	当前是否正在加载
	*/
	bool IsLoading();
```

