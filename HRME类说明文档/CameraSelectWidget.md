### 说明

相机选择对话框，程序启动时，先进入此对话框，填入信息，在打开程序的主界面

### 信号

```c++
	/*
	 * @brief		需要打开相机时，触发该信号
	 * @param[in]	camera_index	相机索引
	 * @param[in]	comport_name	串口名
	 * @note		没有参数的重载信号用于调试
	 */
	void signal_OpenCamera(int camera_index, const QString& comport_name);
	void signal_OpenCamera();

	/*
	 * @brief		连接相机
	 * @param[in]	camera_index	相机索引
	 */
	void signal_ConnectToCamera(int camera_index);
```

### 接口

```c++
	/*
	* @brief	打开相机结束
	*/
	void slot_OpenCameraFinished(bool is_opened);

	/*
	* @brief	打开相机结束，无论打开是否成功
	*/
	void slot_OpenCameraFinished();

	/*
	* @brief	相机被移除了
	*/
	void slot_CameraRemoved();

	/*
	* @brief	有相机添加进来
	*/
	void slot_CameraAdded();

	/*
	 * @brief	获取当前的串口名称
	 */
	QString GetCurrentComportName() const;

```

