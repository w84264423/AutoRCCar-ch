# rc_driver
多线程服务器程序接收视频帧和传感器数据，并允许RC车载驱动器本身具有停车标志，交通灯检测和前碰撞避免能力

## NeuralNetwork
神经网络

### \_\_init\_\_
用的是opencv 自带的 ANN_MLP
```python
self.model = cv2.ANN_MLP()
```
### create

输入层是 38400  -> 32 ,其中 38400 是图片大小
从`mlp_xml/mlp.xml`加载网络结构和参数
```
layer_size = np.int32([38400, 32, 4])
self.model.create(layer_size)
self.model.load('mlp_xml/mlp.xml')
```

### predict
预测下一步的动作


## RCControl
### \_\_init\_\_
初始化控制的端口

### steer 驾驶
根据预测值控制小车的方向
只有四种情况，2：向前，0：向左，1：向右，其他：不动。

### stop 停止
不向控制器发指令

## DistanceToCamera
测量距离
### \_\_init\_\_
初始化一些参数

```python
def __init__(self):
    # camera params
    self.alpha = 8.0 * math.pi / 180
    self.v0 = 119.865631204
    self.ay = 332.262498472
```

### calculate 计算距离
返回目标点到摄像头的距离
不确定这个计算公式

## ObjectDetection


### \_\_init\_\_
目标检测，只检测红绿灯

### detect
1. 摄像头坐标是原点
```python
# y camera coordinate of the target point 'P'
        v = 0
```
2. 最小检测阈值
```python
# minimum value to proceed traffic light state validation
threshold = 150     
```

3. 调用 detectMultiScale ，返回检测结果

`detectMultiScale` 是 opencv的 多目标检测函数
参数详解：
- gray_image 待检测的图片，这里是灰度图
- scaleFactor--表示在前后两次相继的扫描中，搜索窗口的比例系数。默认为1.1即每次搜索窗口依次扩大10%
- minNeighbors 表示构成检测目标的相邻矩形的最小个数(默认为3个)。如果组成检测目标的小矩形的个数和小于 min_neighbors - 1 都会被排除。如果min_neighbors 为 0, 则函数不做任何操作就返回所有的被检候选矩形框，这种设定值一般用在用户自定义对检测结果的组合程序上；
- flags--要么使用默认值，要么使用CV_HAAR_DO_CANNY_PRUNING，如果设置为CV_HAAR_DO_CANNY_PRUNING，那么函数将会使用Canny边缘检测来排除边缘过多或过少的区域
- minSize和maxSize用来限制得到的目标区域的范围。

```python
cascade_obj = cascade_classifier.detectMultiScale(
    gray_image,
    scaleFactor=1.1,
    minNeighbors=5,
    minSize=(30, 30),
    flags=cv2.cv.CV_HAAR_SCALE_IMAGE
)
```
4. 如果是停止信号

5. 检测红绿灯
    1. 对检测框长宽各所有 20 个像素，然后进行高斯模糊
    ```pyhton
    roi = gray_image[y_pos+10:y_pos + height-10, x_pos+10:x_pos + width-10]
    mask = cv2.GaussianBlur(roi, (25, 25), 0)
    ```

    2. 对高斯模糊的图片取像素的最大最小值(最亮最暗值)，如果差值大于阈值，说明有灯亮了，但此时还没确定是什么灯,然后以最亮的点画一个圈，表示灯的范围
    ```python
    (minVal, maxVal, minLoc, maxLoc) = cv2.minMaxLoc(mask)
    if maxVal - minVal > threshold:
      cv2.circle(roi, maxLoc, 5, (255, 0, 0), 2)
    ```

    3. 判断是什么灯

    ```python
    # Red light
    if 1.0/8*(height-30) < maxLoc[1] < 4.0/8*(height-30):
        cv2.putText(image, 'Red', (x_pos+5, y_pos-5), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 2)
        self.red_light = True

    # Green light
    elif 5.5/8*(height-30) < maxLoc[1] < height-30:
        cv2.putText(image, 'Green', (x_pos+5, y_pos - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 0), 2)
        self.green_light = True

    # yellow light
    #elif 4.0/8*(height-30) < maxLoc[1] < 5.5/8*(height-30):
    #    cv2.putText(image, 'Yellow', (x_pos+5, y_pos - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 255), 2)
    #    self.yellow_light = True
    ```

## SensorDataHandler
### handle

处理传感器数据

## VideoStreamHandler
视频流处理

1. 定义神经网络和两个检测器，一个检测stop信号，一个检测红绿灯
```python
model = NeuralNetwork()
model.create()

obj_detection = ObjectDetection()
rc_car = RCControl()

# cascade classifiers
stop_cascade = cv2.CascadeClassifier('cascade_xml/stop_sign.xml')
light_cascade = cv2.CascadeClassifier('cascade_xml/traffic_light.xml')
```
## ThreadServer
线程管理器
这里有两个线程，一个线程管理测距数据，一个线程管理视频流