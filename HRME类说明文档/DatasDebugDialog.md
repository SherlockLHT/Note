### 说明

数据调试窗口，用作开发这调试用

### 接口

```c++
	/*
	 * @brief	接受到一帧图像
	 */
	void slot_ReceiveFrame();

	/*
	 * @brief		接收到一帧图像处理耗时
	 * @param[in]	millsecond	一帧图像处理耗时(ms)
	 */
	void slot_ReceiveOneFrameProcessTime(int millsecond);
```

