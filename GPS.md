adb shell dumpsys location  

Modem 中的 GPS看做是GPS Service；另外一部分在 Ap 里面，我们把这部分看做是GPS Client。Client 主要是通过 QMI 的通信方式接收 Service 发过来的信息，当然 Client也可以通过 QMI 发送信息给 Service，这个就是GPS的最大的框架。  
