

# Celery

![1](pic/1.jpg)



![8](pic/8.jpg)



![9](pic/9.jpg)



![10](pic/10.jpg)



# Celery Architecture

1.  celery client发送message给broker

2.  worker 从broker中消费消息，并将结果存储在result_end中

![11](pic/11.jpg)



![12](pic/12.jpg)



![13](pic/13.jpg)



![14](pic/14.jpg)

![15](pic/15.jpg)

- result 建议使用非关系型数据库

![16](pic/16.jpg)



![1整体过程](pic/1整体过程.png)

# Use

![27](pic/27.jpg)



![31](pic/31.jpg)

![33](pic/33.jpg)

![35](pic/35.jpg)

![37](pic/37.jpg)

![42](pic/42.jpg)

# Celery colony

![manyM](pic/manyM.png)

- Celery Worker：
  在2 台server上部署worker，其中：
  server1上的worker处理queue priority_low和priority_high上的事件
  server2上的worker只处理priority_high上的事件
- Celery Client：在应用中调用
- Rabbit MQ：在server3上启动
- Redis：在localhost启动

![all](pic/all.png)