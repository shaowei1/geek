

# Celery

![1](/home/www/Github/owner/1.jpg)



![8](/home/www/Github/owner/8.jpg)



![9](/home/www/Github/owner/9.jpg)



![10](/home/www/Github/owner/10.jpg)



# Celery Architecture

1.  celery client发送message给broker

2.  worker 从broker中消费消息，并将结果存储在result_end中

![11](/home/www/Github/owner/11.jpg)



![12](/home/www/Github/owner/12.jpg)



![13](/home/www/Github/owner/13.jpg)



![14](/home/www/Github/owner/14.jpg)

![15](/home/www/Github/owner/15.jpg)

- result 建议使用非关系型数据库

![16](/home/www/Github/owner/16.jpg)



![1整体过程](/home/www/Downloads/1整体过程.png)

# Use

![27](/home/www/Github/owner/27.jpg)



![31](/home/www/Github/owner/31.jpg)

![33](/home/www/Github/owner/33.jpg)

![35](/home/www/Github/owner/35.jpg)

![37](/home/www/Github/owner/37.jpg)

![42](/home/www/Github/owner/42.jpg)

# Celery colony

![manyM](/home/www/Downloads/manyM.png)

- Celery Worker：
  在2 台server上部署worker，其中：
  server1上的worker处理queue priority_low和priority_high上的事件
  server2上的worker只处理priority_high上的事件
- Celery Client：在应用中调用
- Rabbit MQ：在server3上启动
- Redis：在localhost启动

![all](/home/www/Downloads/all.png)