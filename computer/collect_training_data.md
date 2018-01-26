# CollectTrainingData
收集训练数据

## \_\_init\_\_

1. 初始化一个socket服务器
```python
self.server_socket = socket.socket()
self.server_socket.bind(('192.168.1.100', 8000))
self.server_socket.listen(0)
```

2. 返回套接字对象
```python
self.server_socket.accept()[0]
```



## collect_image
收集图像

1. `saved_frame = 0` 已经保存的帧
2. `total_frame = 0` 所有的帧

3. 从摄像头收集图像
每个图像的大小是 1 * 38400
图像标签的大小是 1 * 4
```python
# collect images for training
print 'Start collecting images...'
e1 = cv2.getTickCount()
image_array = np.zeros((1, 38400))
label_array = np.zeros((1, 4), 'float')
```

4. 逐帧获取图像

1. 每次先读取 1024 长度的数据
```python
stream_bytes += self.connection.read(1024)
```

2. 找到开始和结束的位置
```python
first = stream_bytes.find('\xff\xd8')
last = stream_bytes.find('\xff\xd9')
```
3. 如果开始和结束的位置都找到了
    图像是
    ```python
    jpg = stream_bytes[first:last + 2]
    ```
    多余的依然是stream_bytes流数据
4. 解码图片，成为numpy数组
```python
image = cv2.imdecode(np.fromstring(jpg, dtype=np.uint8), cv2.CV_LOAD_IMAGE_GRAYSCALE)
```
5. 选择图片的下半部分
```python
roi = image[120:240, :]
```
6. 保存整张图片
```python
cv2.imwrite('training_images/frame{:>05}.jpg'.format(frame), image)
```
7. 展示整张图片
```python
cv2.imshow('image', image)
```
8. 下半部分的图片转换成一维
```python
temp_array = roi.reshape(1, 38400).astype(np.float32)
```
9. 获取用户的输入并执行
复合操作
```python
if key_input[pygame.K_UP] and key_input[pygame.K_RIGHT]:
    ...
```
单个操作
```python
elif key_input[pygame.K_UP]:
    ...
```

如果输入是八个操作之一，那么这个frame 就保存起来，saved_frame += 1，而不管输入的操作是什么，`total_frame += 1`

拼成一个整图片，上半部分是空白的， 下半部分是从摄像头中获取的，标签也类似
```python
image_array = image_array = np.vstack((image_array, temp_array))
label_array = np.vstack((label_array, self.k[1]))
```


10. 把train的图片和label转换为numpy，保存
```python
file_name = str(int(time.time()))
directory = "training_data"
if not os.path.exists(directory):
    os.makedirs(directory)
try:    
    np.savez(directory + '/' + file_name + '.npz', train=train, train_labels=train_labels)
except IOError as e:
    print(e)
```