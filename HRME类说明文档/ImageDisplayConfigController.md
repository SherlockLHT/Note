### 说明

图像显示配置控制器，和ImageDisplayConfigWidget类合并为MVC模式，此类将MC层合并成一个类。

内部包含图像显示的一些配置参数，包括是否缩放、是否显示原始图像、是否开启同步滚动、是否显示帧率等配置。

单例模式，非线程安全，未在多线程中使用。

### 信号

```c++
	/*
	* @brief	当图像缩放配置状态改变时触发
	*/
	void signal_ImageZoomStateChanged(bool is_enabled);

	/*
	* @brief	当显示原始图像状态改变时触发
	*/
	void signal_ShowOriginImageStateChanged(bool is_enabled);

	/*
	 * @brief	当滚动条同步状态改变时触发
	 */
	void signal_ScrollSynchroStateChanged(bool is_synchro);
```

### 接口

```c++
	/*
	 * @brief	设置/获取图像缩放使用
	 */
	bool GetImageZoomEnabled();
	void SetImageZoomEnabled(bool is_enabled);

	/*
	 * @brief	获取/设置是否显示原始图像
	 */
	bool GetShowOriginImage();
	void SetShowOriginImage(bool is_show);

	/*
	 * @brief	获取/设置滚动条是否同步
	 */
	bool GetScrollSynchroEnabled();
	void SetScrollSynchroEnabled(bool is_synchro);

	/*
	 * @brief	获取/设置是否显示帧率
	 */
	bool GetShowFrameRateEnabled();
	void SetShowFrameRateEnabled(bool is_show);

	/*
	 * @brief	获取/设置是否显示灰度值
	 */
	bool GetShowGreyValueEnabled();
	void SetShowGreyValueEnabled(bool is_show);

	/*
	 * @brief	获取/设置是否自动录像
	 */
	bool GetAutoVideotapeEnabled();
	void SetAutoVideotapeEnabled(bool is_auto);

	/*
	 * @brief	获取/设置显示录像状态提示的势能状态
	 */
	bool GetShowRecordStatusTipsEnabled();
	void SetShowRecordStatusTipsEnabled(bool is_show);

	/*
	 * @brief	获取/设置是否显示标尺
	 */
	bool GetShowScaleEnabled();
	void SetShowScaleEnabled(bool is_show);
```

