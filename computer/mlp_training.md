

1. 记录训练数据加载的开始时间
> cv2.getTickCount()函数返回从参考点到这个函数被执行的时钟数
```python
e0 = cv2.getTickCount()
```
2. 载入训练数据
```python
image_array = np.zeros((1, 38400))
label_array = np.zeros((1, 4), 'float')
training_data = glob.glob('training_data/*.npz')
```

3. 对每张图片，空白图像和训练图像上下合并

```python
image_array = np.vstack((image_array, train_temp))
```


4. 输出载入图片的时间
```python
e00 = cv2.getTickCount()
time0 = (e00 - e0)/ cv2.getTickFrequency()
print 'Loading image duration:', time0
```
> cv2.getTickFrequency()返回时钟频率。

5. 7:3 分给训练和测试数据
```python
train, test, train_labels, test_labels = train_test_split(X, y, test_size=0.3)
```

6. 记录训练的开始时间
```python
e1 = cv2.getTickCount()
```

7. 创建一个多层感知机

```python
# create MLP
layer_sizes = np.int32([38400, 32, 4])
model = cv2.ANN_MLP()
model.create(layer_sizes)

8. 指定停止条件
```python
criteria = (cv2.TERM_CRITERIA_COUNT | cv2.TERM_CRITERIA_EPS, 500, 0.0001)
#criteria2 = (cv2.TERM_CRITERIA_COUNT, 100, 0.001)
9. 设置一些参数
params = dict(term_crit = criteria,
               train_method = cv2.ANN_MLP_TRAIN_PARAMS_BACKPROP,
               bp_dw_scale = 0.001,
               bp_moment_scale = 0.0 )
```
10. 进行训练
```python
num_iter = model.train(train, train_labels, None, params = params)
```
11. 记录训练停止时间，输出训练持续时间
```python
e2 = cv2.getTickCount()
time = (e2 - e1)/cv2.getTickFrequency()
print 'Training duration:', time
#print 'Ran for %d iterations' % num_iter
```


12. 在训练集中的预测准确率
```python
ret_0, resp_0 = model.predict(train)
prediction_0 = resp_0.argmax(-1)
true_labels_0 = train_labels.argmax(-1)

train_rate = np.mean(prediction_0 == true_labels_0)
print 'Train accuracy: ', "{0:.2f}%".format(train_rate * 100)
```

13. 在测试数据的预测准确率
```python
ret_1, resp_1 = model.predict(test)
prediction_1 = resp_1.argmax(-1)
true_labels_1 = test_labels.argmax(-1)

test_rate = np.mean(prediction_1 == true_labels_1)
print 'Test accuracy: ', "{0:.2f}%".format(test_rate * 100)
```
