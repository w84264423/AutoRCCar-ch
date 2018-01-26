# ultrasonic_client
作用是发送传感器测量的距离数据到主机


1. 初始化一个socket客户端， 绑定的到一个地址
```python
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(('192.168.1.100', 8000))
```

## measure 测量距离
原理是：
1. 间隔 0.00001 向外发送两个信号，
```python
GPIO.output(GPIO_TRIGGER, True)
time.sleep(0.00001)
GPIO.output(GPIO_TRIGGER, False)
```
2. 计算两次信号返回的时间间隔
```python
elapsed = stop-start
```
3. （间隔时间 * 速度） / 2  = 距离
```python
distance = (elapsed * 34300)/2
```