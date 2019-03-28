### 说明

相机设置界面，位于ConfigDialog中，包含相机的亮度、快门、增益、gamma等参数的设置界面以及对参数的固定保存功能，设置位固定后，下次开机，会读取保存的参数设置到相机

### 接口

```c++
/*
	 * @brief	绑定相机管理实例
	 */
	void BindingCameraManagerInstance(PointGreyManager* grey_point_manager);

	/*
	 * @brief	更新一帧图像信息
	 */
	void slot_UpdateFrameImageInfo();
```

