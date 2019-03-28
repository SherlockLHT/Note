### 说明

主程序窗口，继承自QMainWindow，作为主程序的最主要的窗口存在，内部也包含少量的逻辑，和一些子类的信号转发

### 信号

```c++
	/*
	 * @brief		发送当前相机的数量
	 * @param[in]	count	数量
	 */
	void signal_CameraCount(unsigned int count);

	/*
	 * @brief		当打开相机完成时，触发
	 * @param[in]	is_opened	相机是否打开成功
	 */
	void signal_OpenCameraFinished(bool is_opened);

	/*
	 * @brief	需要返回相机选取界面的时候触发
	 */
	void signal_ReturnCameraSelectDialog();

	/*
	 * @brief	需要获取相机数量是触发
	 */
	void signal_GetCameraNumber();

	/*
	 * @brief		当切换暂停/继续状态是触发
	 * @param[in]	is_capturing	当前是否在取样
	 */
	void signal_PauseContinueStatus(bool is_capturing);

	/*
	 * @brief		发送录像状态，在开始/停止录像时触发
	 * @param[in]	is_videotaping	是否正在录像
	 */
	void signal_VideotapStatus(bool is_videotaping);
```

### 接口

```c++
	/*
	 * @brief	获取灰点相机管理实例
	 */
	PointGreyManager* GetPointGreyManagerInstance();

	/*
	 * @brief		连接相机
	 * @param[in]	camera_index	相机索引
	 */
	void slot_ConnectCamera(int camera_index);

	/*
	 * @brief		打开相机
	 * @param[in]	camera_index	相机索引
	 * @param[in]	comport_name	串口名
	 * @note		没有参数的重载函数用于调试
	 */
	bool slot_OpenCamera(int camera_index, const QString& comport_name);
	void slot_OpenCamera();

	/*
	 * @brief		接受到灰度值
	 * @param[in]	grey_value	灰度值
	 */
	void slot_ReceiveGreyValue(double grey_value);

	/*
	 * @brief	点击返回动作
	 */
	void slot_OnClickReturnAction();

	/*
	 * @brief	退出程序
	 */
	void slot_QuitProgram();

	/*
	 * @brief	点击录制视频动作
	 */
	void slot_OnClickRecordVideoAction();

	/*
	 * @brief	点击截屏动作
	 */
	void slot_OnClickScreenShotAction();

	/*
	 * @brief	点击暂停继续动作
	 */
	void slot_OnClickPauseContinueAction();

	/*
	 * @brief	重新打开窗口
	 */
	void slot_ReopenComport();

	/*
	 * @brief	点击加载图片动作
	 */
	void slot_OnClickLoadImageAction();

	/*
	 * @brief	点击加载视频动作
	 */
	void slot_OnClickLoadVideoAction();

	/*
	 * @brief	暂停视频播放
	 */
	void slot_PausePlayVideo();

	/*
	 * @brief	展示指定图像
	 */
	void slot_ShowImage(const QString& image);

	/*
	 * @brief	有相机加入
	 */
	void slot_CameraAdded();

	/*
	 * @brief		展示保存图片结果
	 * @param[in]	is_ok		操作是否成功
	 * @param[in]	image_path	最后一张图片路径
	 * @param[in]	tips		需要显示在界面的提示文字
	 */
	void slot_ShowSaveImageResult(bool is_ok, const QString& image_path, const QString& tips);

```

