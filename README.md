## AutoRCCar [中文]
查看运动中的小车（YouTube视频，显示不了实属正常）
<a href="http://www.youtube.com/watch?feature=player_embedded&v=BBwEF6WBUQs
" target="_blank"><img src="http://img.youtube.com/vi/BBwEF6WBUQs/0.jpg" width="360" height="240" border="10" /></a>


这是一个用 RC 小车、树莓派、Arduino和开源软件实现的小规模的自动驾驶项目。
系统使用了Raspberry Pi带着一个摄像头和一个用来测距的超声传感器，一个主机操作驾驶方向，物体识别（这里识别的是停止标志和红绿灯）、目标物测距，一个Arduino board用来控制小车

## 依赖
* Raspberry Pi: 
  - Picamera
* Computer:
  - Numpy
  - OpenCV 2.4.10.1
  - Pygame
  - PiSerial


## 目录结构
- raspberrt_pi
  - stream_client.py：以jpeg格式将视频帧流式传输到主机
  - ultrasonic_client.py：将由传感器测量的距离数据发送到主机
- Arduino
    - rc_keyboard_control.ino：作为rc控制器和计算机之间的接口，允许用户通过USB串行接口发送命令
- 电脑
  - cascade_xml
    训练级联分类器xml文件
  - 棋盘
    用于校准的图像，由pi相机捕获
  - training_data
    以npz格式训练神经网络的图像数据
  - testing_data
    以npz格式测试神经网络的图像数据
  - training_images
    在图像训练数据采集阶段保存视频帧（可选）
  - mlp_xml
    在xml文件中训练神经网络参数
  - rc_control_test.py：带键盘的驱动RC车（测试目的）
  - picam_calibration.py：pi相机校准，返回相机矩阵
  - collect_training_data.py：接收流式视频帧和标签框以供后续培训
  - mlp_training.py：神经网络训练
  - mlp_predict_test.py：用测试数据测试训练有素的神经网络
  - rc_driver.py：多线程服务器程序接收视频帧和传感器数据，并允许RC车载驱动器本身具有停车标志，交通灯检测和前碰撞避免能力

## 如何开车

1. Flash Arduino：Flash “rc_keyboard_control.ino”到Arduino并运行“rc_control_test.py”来驱动rc车用键盘（测试目的）

1. Pi相机校准：使用pi相机以各种角度拍摄多张棋盘图像，并将其放入“chess_board”文件夹中，运行“picam_calibration.py”，并返回相机矩阵，这些参数将用于“rc_driver.py”

1. 收集培训数据和测试数据：首先运行“collect_training_data.py”，然后在raspberry pi上运行“stream_client.py”。用户按键盘驱动RC车，只有当有按键动作时才保存框架。完成驾驶后，按“q”退出，数据保存为npz文件。

1. 神经网络训练：运行“mlp_training.py”，取决于所选择的参数，需要一些时间训练。培训后，参数保存在“mlp_xml”文件夹中

1. 神经网络测试：运行“mlp_predict_test.py”从“test_data”文件夹加载测试数据，并从“mlp_xml”文件夹中的xml文件中训练参数

1. 级联分类器训练（可选）：训练有素的停车标志和交通灯分类器包含在“cascade_xml”文件夹中，如果您有兴趣培训您自己的分类器，请参考OpenCV文档和Thorsten Ball

1. 自驾驾驶：首先运行“rc_driver.py”在计算机上启动服务器，然后在raspberry pi上运行“stream_client.py”和“ultrasonic_client.py”。

> 原项目是适用于python 2.7 本项目改成适用于 python3

> test 文件夹下讲解不丰富，待改进

---
## AutoRCCar [English]

See self-driving in action

<a href="http://www.youtube.com/watch?feature=player_embedded&v=BBwEF6WBUQs
" target="_blank"><img src="http://img.youtube.com/vi/BBwEF6WBUQs/0.jpg" width="360" height="240" border="10" /></a>

  A scaled down version of self-driving system using a RC car, Raspberry Pi, Arduino and open source software. The system uses a Raspberry Pi with a camera and an ultrasonic sensor as inputs, a processing computer that handles steering, object recognition (stop sign and traffic light) and distance measurement, and an Arduino board for RC car control.
  
### Dependencies
* Raspberry Pi: 
  - Picamera
* Computer:
  - Numpy
  - OpenCV 2.4.10.1
  - Pygame
  - PiSerial
  
### About
- raspberrt_pi/ 
  -	***stream_client.py***: stream video frames in jpeg format to the host computer
  -	***ultrasonic_client.py***: send distance data measured by sensor to the host computer
- arduino/
  -	***rc_keyboard_control.ino***: acts as a interface between rc controller and computer and allows user to send command via USB serial interface
- computer/
  -	cascade_xml/ 
    - trained cascade classifiers xml files
  -	chess_board/ 
    - images for calibration, captured by pi camera 
  -	training_data/ 
    - training data for neural network in npz format
  -	training_images/ 
    - saved video frames during image training data collection stage (optional)
  -	mlp_xml/ 
    - trained neural network parameters in a xml file
  -	***picam_calibration.py***: pi camera calibration, returns camera matrix
  -	***collect_training_data.py***: receive streamed video frames and label frames for later training
  -	***mlp_training.py***: neural network training
  -	***rc_driver.py***: a multithread server program receives video frames and sensor data, and allows RC car drives by itself with stop sign, traffic light detection and front collision avoidance capabilities
- test/
  -	***rc_control_test.py***: RC car control with keyboard 
  -	***stream_server_test.py***: video streaming from Pi to computer
  -	***ultrasonic_server_test.py***: sensor data streaming from Pi to computer
- Traffic_signal/ 
  - trafic signal sketch contributed by [@geek111](https://github.com/geek1111)


### How to drive
1. **Flash Arduino**: Flash *“rc_keyboard_control.ino”* to Arduino and run *“rc_control_test.py”* to drive the rc car with keyboard (testing purpose)

2. **Pi Camera calibration:** Take multiple chess board images using pi camera at various angles and put them into “chess_board” folder, run *“picam_calibration.py”* and it returns the camera matrix, those parameters will be used in *“rc_driver.py”*

3. **Collect training data and testing data:** First run *“collect_training_data.py”* and then run *“stream_client.py”* on raspberry pi. User presses keyboard to drive the RC car, frames are saved only when there is a key press action. When finished driving, press “q” to exit, data is saved as a npz file. 

4. **Neural network training:** Run *“mlp_training.py”*, depend on the parameters chosen, it will take some time to train. After training, model will be saved in “mlp_xml” folder

5. **Cascade classifiers training (optional):** trained stop sign and traffic light classifiers are included in the "cascade_xml" folder, if you are interested in training your own classifiers, please refer to [OpenCV documentation](http://docs.opencv.org/doc/user_guide/ug_traincascade.html) and [this great tutorial by Thorsten Ball](http://coding-robin.de/2013/07/22/train-your-own-opencv-haar-classifier.html)

6. **Self-driving in action**: First run *“rc_driver.py”* to start the server on the computer and then run *“stream_client.py”* and *“ultrasonic_client.py”* on raspberry pi. 

