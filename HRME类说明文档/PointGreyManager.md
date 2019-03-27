### 说明

灰点相机管理类，内部封装了所有针对相机的操作，包括：相机的检测、连接、断开、取像、获取属性、设置属性、获取相机库版本。

### 信号

```c++
	/*
	 * @brief		已经连接的相机被移除是触发
	 * @param[in]	is_capturing	移除的时候，是否正在取样
	 */
	void signal_CameraRemoved(bool is_capturing);

	/*
	 * @brief	有新相机加入时触发
	 */
	void signal_CameraAdded();

	/*
	 * @brief	接受到一帧新的图像
	 */
	void signal_ReceiveFrame();

	/*
	 * @brief	开始读取图像信号
	 */
	void signal_StartReadImage();
```

### 接口

```c++
	/*
	 * @brief		连接相机
	 * @param[in]	index	相机index
	 * @note		连接相机耗时较长，预先连接可以减少打开程序的等待时间
	 */
	bool ConnectToCamera(unsigned int index = 0);

	/*
	 * @brief	断开当前已经连接的相机，若未连接则跳过
	 */
	bool DisconnectCamera();

	/*
	 * @brief 		是否已经连接相机
	 * @param[in]	camera_index	指定相机索引
	 */
	bool IsConnect() const;
	bool IsConnect(int camera_index) const;

	/*
	 * @brief	获取当前相机的索引
	 * @return	-1表示当前没有相机连接
	 */
	int GetCurrentCameraIndex() const;

	/*
	 * @brief		获取相机信息
	 * @param[out]	camera_info	相机信息
	 */
	bool GetCameraInfomation(int camera_index, 
							PointGreyStructure::CameraInfomation* camera_infomation);

	/*
	 * @brief	开始采集图像
	 */
	bool StartCapture();

	/*
	 * @brief	停止采集图像
	 */
	bool StopCapture();

	/*
	 * @brief	是否正在采集图像
	 */
	bool IsCaptureing() const;

	/*
	 * @brief	获取最近一次的错误信息
	 * @note	上层封装的错误提示
	 */
	QString GetLastErrorMessage() const;

	/*
	 * @brief	获取PC上连接的camera的数量
	 * @note	获取失败返回0
	 */
	unsigned int GetCameaNumber();

	/*
	 * @brief		需获取相机属性信息
	 * @param[in]	property_type	需要获取的属性类型的信息
	 * @param[out]	property_info	具体属性的信息
	 */
	bool GetCameraPropertyInfo(PointGreyStructure::PropertyType property_type, 
		PointGreyStructure::PropertyInfo* property_info);

	/*
	 * @brief		获取相机属性
	 * @param[in]	property_type	需要获取的属性类型
	 * @param[out]	property		具体属性
	 */
	bool GetCameraProperty(PointGreyStructure::PropertyType property_type, 
		PointGreyStructure::Property* out_property);

	/*
	 * @brief		设置相机属性
	 * @param[in]	property_type	需要设置的属性类型
	 * @param[in]	property		需要设置的具体属性
	 */
	bool SetCameraProperty(PointGreyStructure::PropertyType property_type, 
		const PointGreyStructure::Property& new_property);

	/*
	 * @brief	显示相机控制对话框
	 */
	void ShowCameraControlDialog();

	/*
	* @brief	获取图像尺寸
	*/
	QSize GetResolution();

	/*
	 * @brief	在断线前，相机是否正在取样
	 */
	bool IsCaptureingBeforeDisconnectd();

	/*
	* @brief	检测一次相机
	*/
	unsigned int DetectCamera();

	/*
	 * @brief		获取库版本
	 * @param[out]	library_version	库版本
	 */
	static bool GetLibraryVersion(QString* library_version);
```

