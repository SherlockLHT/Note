### 说明

图片保存接口，使用 **opencv** 的图片保存功能，调用此模块接口可直接保存将 **opencv** 的 **mat** 数据类型保存成图片

### 接口

```c++
	/*
	 * @brief	获取最近一次的错误
	 */
	std::string GetLastError() const;

	/*
	 * @brief		保存图片文件
	 * @param[in]	image		需要保存的图像
	 * @param[in]	file_path	图片的文件路径
	 */
	bool SaveImageFile(const cv::Mat& image, const std::string& file_path);

	/*
	 * @brief		保存PGM格式图片
	 * @param[in]	is_save_as_binary	是否保存位二进制文件
	 */
	bool SavePGMImage(const cv::Mat& image, const std::string& file_path, bool is_save_as_binary = true);

	/*
	* @brief		批量保存PGM图像
	* @param[in]	images			需要保存的所有图像
	* @param[in]	file_path		保存路径
	* @param[out]	last_picture	最后一张保存成功的图像
	* @param[in]	count			需要保存的数量，若没有指定，则保存传入的所有图像
	* @return		只要有一张保存成功，则返回true
	*/
	bool SavePGMImages(const QVector<cv::Mat*>& images, const std::string& file_path, std::string* last_picture,
		bool is_save_as_binary = true, int count = -1);

	/*
	 * @brief	保存位BMP格式图片
	 */
	bool SaveBMPImage(const cv::Mat& image, const std::string& file_path);

	/*
	 * @brief		批量保存BMP图像
	 * @param[in]	images			需要保存的所有图像
	 * @param[in]	file_path		保存路径
	 * @param[out]	last_picture	最后一张保存成功的图像
	 * @param[in]	count			需要保存的数量，若没有指定，则保存传入的所有图像
	 * @return		只要有一张保存成功，则返回true
	 */
	bool SaveBMPImages(const QVector<cv::Mat*>& images, const std::string& file_path, std::string* last_picture, int count = -1);

	/*
	 * @brief		保存位JPEG格式图片
	 * @param[in]	quality	图片质量，0~100，越高质量越好，默认95
	 */
	bool SaveJPEGImage(const cv::Mat& image, const std::string& file_path, unsigned short quality = 95);

	/*
	 * @brief 
	 * @param[in]	images			需要保存的所有图像
	 * @param[in]	file_path		保存路径
	 * @param[out]	last_picture	最后一张保存成功的图像
	 * @param[in]	quality			图片质量
	 * @param[in]	count			需要保存的数量，若没有指定，则保存传入的所有图像
	 * @return		只要有一张保存成功，则返回true
	 */
	bool SaveJPEGImages(const QVector<cv::Mat*>& images, const std::string& file_path, std::string* last_picture, 
		unsigned short quality = 95, int count = -1);

	/*
	 * @brief		保存位PNG格式图片
	 * @param[in]	compensation_level	压缩等级，0~9，越高，文件越小，压缩时间越长，默认3	
	 */
	bool SavePNGImage(const cv::Mat& image, const std::string& file_path, unsigned short compensation_level = 3);

	/*
	 * @brief 
	 * @param[in]	images				需要保存的所有图像
	 * @param[in]	file_path			保存路径
	 * @param[out]	last_picture		最后一张保存成功的图像
	 * @param[in]	compensation_level	压缩等级
	 * @param[in]	count				需要保存的数量，若没有指定，则保存传入的所有图像
	 * @return		只要有一张保存成功，则返回true
	 */
	bool SavePNGImages(const QVector<cv::Mat*>& images, const std::string& file_path, std::string* last_picture, 
		unsigned short compensation_level = 3, int count = -1);

	/*
	 * @brief	获取所有支持的图片格式
	 * @note	顺序按照定内置的格式
	 */
	static void GetAllPictureFormat(std::vector<std::string>* all_image_type);

	/*
	 * @brief		修改路径
	 * @param[in]	path			原始路径
	 * @param[in]	replace_str		替换的字符串
	 * @param[in]	index			插入路径的序号
	 */
	static std::string ModefiyPath(const std::string& path, const std::string& replace_str, int index);
```

