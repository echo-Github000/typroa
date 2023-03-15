#### mesh设备连接试验

1. 试验环境

![image-20230315100104171](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230315100104171.png)







2. 试验结果

​	试验时间：3-15 1:30 - 9:30 一共8个小时

​	心跳期望结果：3分钟一次心跳，8小时的期望结果应为160次



​	实际心跳结果：设备B实际心跳130次，丢失30次，触发16次send special attribute request，但未曾离线；设备A实际心跳154次，丢失6次，触发5次send special attribute request，但未曾离线；设备B触发16次send special attribute request后上报属性中携带的ttl值12次为4，即经过A中继，rssi为-80左右，4次为5，即未经过中继，rssi为-90左右；设备A触发5次send special attribute request后上报属性中携带的ttl值4次为5，即未经过中继，rssi为-80左右，1次为4，即经过B中继，rssi为-90左右。

​	设备B心跳130次，心跳初始值均为5，hops值54次为1，76次为2（中继）；设备A心跳154次，心跳初始值均为5，hops值140次为1，14次为2（中继）；