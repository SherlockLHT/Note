### 说明

串口管理模块，有关串口的所有操作都封装在此类中，包括握手、对焦状态、灰度值的发送/获取

### 信号

```c++
	/*
	 * @brief	当获取到下位机握手信号之后触发该信号
	 */
	void signal_ComportConnectStatus(bool is_connected);

	/*
	 * @brief		对焦状态改变时触发
	 * @param[in]	is_focusing	是否正在对焦
	 */
	void signal_FocusStateChanged(bool is_focusing);
```

### 接口

```c++
	/*
	 * @brief 设置/获取串口名
	 */
	void SetPortName(const QString& port_name);
	QString GetSerialPortName();

	/*
	 * @brief	打开串口
	 * @return	是否打开成功
	 */
	bool OpenComport();

	/*
	 * @brief	关闭串口
	 */
	void CloseComport();

	/*
	 * @brief		发送数据
	 * @param[in]	send_datas	需要发送的数据
	 */
	void SendDatas(const QByteArray& send_datas);

	/*
	 * @brief	发送数值
	 */
	void SendValue(int value);

	/*
	 * @brief   
	 QByteArray转int
	 */
	static int Byte2Int(QByteArray bytes);

	/*
	 * @brief       int_data转QByteArray
	 * @param[in]   int_data    需要转换的数据
	 * @param[in]   bits        控制位数
	 */
	static QByteArray Int2Byte(int int_data, int bits = 4);

	/*
	* @brief	获取当前可用的串口名称列表
	*/
	static QStringList GetAvailablePortList();
```

