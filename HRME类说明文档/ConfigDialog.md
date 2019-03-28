### 说明

配置对话框，内部包含图片视频配置窗口、算法配置窗口、程序配置窗口、显示配置窗口和相机设置窗口。

### 接口

```c++
	/*
	 * @brief	获取程序配置的窗体指针
	 */
	ProgramConfigWidget* GetProgramConfigWidget() const;

	/*
	 * @brief	获取算法配置的窗体指针
	 */
	AlgorithmConfigWidget* GetAlogorithmConfigWidget() const;

	/*
	 * @brief	获取相机设置窗口指针
	 */
	CameraSettingWidget* GetCameraSettingWidget() const;
```

