#### MQTT学习笔记



1. 几个异步回调函数的作用。

   ```c
   LIBMQTT_API int MQTTClient_setCallbacks(MQTTClient handle, void* context, MQTTClient_connectionLost* cl,MQTTClient_messageArrived* ma, MQTTClient_deliveryComplete* dc);
   ```

​	connlost函数指针，是当MQTT意外断开连接是调用的函数，用户自己实现

​	msgarrved函数指针，是当服务器有消息推送回来时，客户端在此处接受服务端消息的内容

​	delivered函数指针，是消息送达的回调函数，表明消息已经交互成功

2. MQTT传输数据时，最好先拷贝一份再做后续操作？

![avatar](消息拷贝.png)