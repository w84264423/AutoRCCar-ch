# RCTest
测试带键盘的RC小车

## \_\_init\_\_
连接到小车
```python
self.ser = serial.Serial('/dev/tty.usbmodem1421', 115200, timeout=1)
self.send_inst = True
```

## steer
驾驶

进入操作的循环
```python
while self.send_inst:
```
按照键盘输入进行操作
