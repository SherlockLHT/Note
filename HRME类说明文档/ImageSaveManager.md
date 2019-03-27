### 说明

图像保存管理类，直接在主线程中保存图片，会导致界面短暂卡死，所以这里加了一个保存图片管理类，将保存图片的接口包在此类中，使用多线程保存图片

### 信号

```c++
/*
	 * @brief		保存图像完成之后触发该信号
	 * @param[in]	is_ok			是否保存成功
	 * @param[in]	images_path		最后一张图片的路径
	 * @param[in]	tips			需要显示在界面上的提示文字
	 */
	void signal_FinishSaveImages(bool is_ok, const QString& images_path, const QString& tips);

	/*
	 * @brief	开始线程
	 */
	void signal_StartThread();
```

### 接口

```c++
	/*
	 * @brief	保存图片
	 */
	void SaveImages();
```

