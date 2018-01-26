# picam_calibration

pi相机校准，返回相机矩阵

1. 终端校准

    迭代停止的模式选择，这是一个含有三个元素的元组型数。格式为（type,max_iter,epsilon） 
    ```python
    criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 30, 0.001)
    ```
    - `cv2.TERM_CRITERIA_EPS` 精确度（误差）满足epsilon停止
    - `cv2.TERM_CRITERIA_MAX_ITER`  迭代次数超过max_iter停止

2. 棋盘表格
    ```python
    # 6x9 chess board, prepare object points, like (0,0,0), (1,0,0), (2,0,0) ....,(6,5,0)
    object_point = np.zeros((6*9, 3), np.float32)
    object_point[:, :2] = np.mgrid[0:9, 0:6].T.reshape(-1, 2)
    ```
3. 三维中的点，图像中的点
    ```python
    # 3d point in real world space
    object_points = []
    # 2d points in image plane
    image_points = []
    h, w = 0, 0
    ```

4. 载入棋盘图片
    ```python
    images = glob.glob('chess_board/*.jpg')
    ```

5. 对每张棋盘图片
    1. 读取图片，转成灰度图，拿到宽和高
        ```python
        image = cv2.imread(file_name)
        gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        h, w = gray.shape[:2]
        ```

    2. 寻找棋盘图的内角点位置
        ```python
        ret, corners = cv2.findChessboardCorners(gray, (9, 6), None)
        ```
        (9, 6): 棋盘图中每行和每列角点的个数。
    3. 如果找到了内角点
        1. 保存三维坐标、通过 cornerSubPix 得到平面坐标，然后保存
            ```python
            object_points.append(object_point)
            cv2.cornerSubPix(gray, corners, (11, 11), (-1, -1), criteria)
            image_points.append(corners)
            ```
        2. 在图片中展示找到的点
            ```python
            cv2.drawChessboardCorners(image, (9, 6), corners, ret)
            cv2.imshow('image', image)
            cv2.waitKey(500)
            ```
6. 校准坐标
```python
retval, cameraMatrix, distCoeffs, rvecs, tvecs = cv2.calibrateCamera(object_points, image_points, (w, h), None, None)
```
  返回的结果：
  - `retval` 标定结果
  - `cameraMatrix` 相机的内参数矩阵
  - `distCoeffs` 畸变系数
  - `rvecs` 旋转矩阵
  - `tvecs` 平移向量