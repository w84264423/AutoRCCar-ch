# stream_client
作用是发送摄像头的图像到主机

1. 初始化一个socket客户端， 绑定的到一个地址
```python
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(('192.168.1.100', 8000))
```

2. picamera 的摄像头设定

```python
camera.resolution = (320, 240)      # pi 摄像头的分辨率
camera.framerate = 10               # 每秒钟的帧率
time.sleep(2)                       # 给摄像头两秒钟初始化
start = time.time()
stream = io.BytesIO() # 获取流
```

3. 把摄像头的数据作为jpeg 图像传给主机
```python
for foo in camera.capture_continuous(stream, 'jpeg', use_video_port = True):
    connection.write(struct.pack('<L', stream.tell()))
    connection.flush()
    stream.seek(0)
    connection.write(stream.read())
    if time.time() - start > 600:
        break
    stream.seek(0)
    stream.truncate()
connection.write(struct.pack('<L', 0))
```