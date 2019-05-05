



# Celery

![1](pic/1.jpg)



![8](pic/8.jpg)



![9](pic/9.jpg)

- 给用户发送验证发送邮件
- 发送短信验证码
- 定时任务，比如每天定时统计网站的注册人数，也可以交给Celery周期性的处理。



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

# 代码结构



![jiegou](pic/jiegou.png)

## main.py

```python
from celery import Celery
from kombu import Queue
import time


app = Celery('tasks', backend='redis://127.0.0.1:6379/6')
app.config_from_object('celeryconfig')

class CallbackTask(Task):
    def on_success(self, retval, task_id, args, kwargs):
        print "----%s is done" % task_id

    def on_failure(self, exc, task_id, args, kwargs, einfo):
        pass
```

## task1.py

```python
from celery_app import app

@app.task(base=CallbackTask) 
def add(x, y):
    return x + y
```



## task2.py

```python
from celery_app import app

@app.task(base=CallbackTask) 
def multiply(x,y):
    return x * y

```

## celeryconfig.py

```python
from celery.schedules import crontab
from datetime import timedelta
from kombu import Queue
from kombu import Exchange

result_serializer = 'json'

broker_url = "redis://192.168.1.2"
result_backend = "mongodb://192.168.1.2/celery"
timezone = "Asia/Shanghai"

imports = (
    'celery_app.task1',
    'celery_app.task2'
)

beat_schedule = {
    'add-every-20-seconds': {
        'task': 'celery_app.task1.multiply',
        'schedule': timedelta(seconds=20),
        'args': (5, 7)
    },
    'add-every-10-seconds': {
        'task': 'celery_app.task2.add',
         'schedule': crontab(hour=9, minute=10)
        'schedule': timedelta(seconds=10),
        'args': (23, 54)
    }
}

task_queues = (
    Queue('default', exchange=Exchange('default'), routing_key='default'),
    Queue('priority_high', exchange=Exchange('priority_high'), routing_key='priority_high'),
    Queue('priority_low', exchange=Exchange('priority_low'), routing_key='priority_low'),
)

task_routes = {
    'celery_app.task1.multiply': {'queue': 'priority_high', 'routing_key': 'priority_high'},
    'celery_app.task2.add': {'queue': 'priority_low', 'routing_key': 'priority_low'},
}

# 每分钟最大速率
# task_annotations = {
#     'task2.multiply': {'rate_limit': '10/m'}
# }

```



# Deploy

![all](pic/all.png)

## Celery Server and Client

### Worker on Server1

消费priority_high事件

```bash
celery -A  celery_app.main worker -Q priority_high --concurrency=4 -l info -E -n worker1@%h
```

### Worker on Server2

消费priority_high和priority_low事件

```bash
celery -A celery_app.main worker -Q priority_high,priority_low --concurrency=4 -l info -E -n worker2@%h
```

### Client    test.py

```python
from celery_app.task1 import add
from celery_app.task1 import multiply

for i in range(50):
    add.delay(2, 2)
    multiply.delay(10,10)
```

![eleyrun](pic/0save.png)



# 监控

```python
pip install flower
celery flower --broker=amqp://guest:guest@192.168.xx.xxx:5672//
```

```
http://server2_ip:5555
```

![](pic/jinkong0.png)





![](pic/jinkong1.png)