### 说明

此类中包含了一些常用的工具函数，比如opencv的mat数据类型、灰点相机的图像数据类型和qt的QImage三个数据类型之间的转换方式，以及一些其他的工具函数等

### 接口

```c++
	/*
	 * @brief cv::Mat to QImage
	 */
	static QImage Mat2QImage(const cv::Mat& mat);

	/*
	 * @brief	QImage to cv::Mat
	 */
	static cv::Mat QImage2Mat(const QImage& image);

	/*
	 * @brief	cv::Mat to FlyCapture2::Image
	 */
	static bool ConvertMat2FlyCapture2Image(const cv::Mat& cv_image, FlyCapture2::Image* image);

	/*
	 * @brief	cv::IplImage to FlyCapture::的Image
	 */
	static bool ConvertIplImageToImage(const IplImage* cv_image, FlyCapture2::Image* image);

	/*
	 * @brief	cv::IplImage to QImage
	 */
	static QImage ConverLplImage2QImage(const IplImage* lpl_image);

	/*
	 * @brief	QImage to cv::LplImage
	 */
	static void ConverQImage2LplImage(const QImage& image, IplImage* lpl_image);

	/*
	 * @brief	判断是否是偶数
	 */
	static bool IsEvenNumber(int number);

	/*
	 * @brief		截取分辨率
	 * @param[in]	resolution_str	分辨率的字符串形式
	 * @param[out]	width			宽
	 * @param[out]	height			高
	 */
	static bool SpliteResolution(const QString& resolution_str, int* width, int* height);

	/*
	 * @brief		处理文件名
	 * @param[in]	origin_file_name	原始文件名
	 */
	static QString ProcessFileName(const QString& origin_file_name);

	/*
	 * @brief		检查字符串是否有特定结尾
	 * @param[in]	src_string	源字符串
	 * @param[in]	tail_string	尾字符串
	 */
	static bool EndsWith(const std::string& src_string, const std::string& tail_string);

	/*
	 * @brief		检查字符串是否以特定字符串开头
	 * @param[in]	src_string	源字符串
	 * @param[in]	head_string	头字符串	
	 */
	static bool StartWith(const std::string& src_string, const std::string& head_string);

```

