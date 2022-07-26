adb shell dumpsys location  

Modem 中的 GPS看做是GPS Service；另外一部分在 Ap 里面，我们把这部分看做是GPS Client。Client 主要是通过 QMI 的通信方式接收 Service 发过来的信息，当然 Client也可以通过 QMI 发送信息给 Service，这个就是GPS的最大的框架。  

gps数据从modem到AP  
从loc_api层的locClientIndCb开始走起：vendor/qcom/opensource/location/loc_api/loc_api_v02/loc_api_v02_client.c  

AGPS（Assisted GPS）辅助全球卫星定位系统是结合GSM或GPRS与传统卫星定位，利用基站发送辅助卫星信息以缩减GPS芯片获取卫星信号的延迟时间。  

![GPS框架](https://github.com/Meiliaodasi/Prepare/blob/master/image/GPS%E6%A1%86%E6%9E%B6.png?raw=true)

