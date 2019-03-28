### 说明

图像缓存管理模块，经过算法处理完成的图像会保存在此模块内，最多同时保存20张。

此模块的是为保存图片做缓存使用，当保存图片时，保存图片模块将会从此模块中取出缓存的多张图像，进行图片保存操作。

此模块采用单例模式，且线程安全。

### 接口

```c++
	/*
	 * @brief		追加图像
	 * @param[in]	image	一帧图像
	 */
	void AppendImage(const cv::Mat& image);

	/*
	 * @brief		获取缓存图像
	 * @param[out]	out_images	缓存图片
	 * @note		out_images需要自行开辟内存，function内部对已开辟的内存进行深拷贝
	 * @return		获取成功的图像的总帧数
	 */
	int GetCacheImages(QVector<cv::Mat*>& out_images);

	/*
	 * @brief		分配图像空间
	 * @param[in]	images	图像集合
	 * @param[in]	size	图像数量
	 */
	static void ReserveImagesVector(QVector<cv::Mat*>& images, int size);
```

