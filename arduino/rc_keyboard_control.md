# rc_keyboard_control

帮助用户在用键盘控制小车

## 用数字键控制方向
<!-- - 6: 右
- 7: 左
- 10: 前
- 9: 后 -->
- 不太确定意思

```
int right_pin = 6;  
int left_pin = 7;
int forward_pin = 10;
int reverse_pin = 9;
```

## setup
配置并启动

## loop
进入循环

如果有命令就执行命令，
没有命令就reset


## 以下是一系列操纵小车运动方向的函数

- `right` 向右
    按下向右的键，并推迟time发送命令
    ```
    digitalWrite(right_pin, LOW);
    delay(time);
    ```
- `left`  向左
- `forward`  向前
- `reverse`  向后
- `forward_right`  向右前方
    相当于同时按下向前的键和向右的键
    ```
    digitalWrite(forward_pin, LOW);
    digitalWrite(right_pin, LOW);
    ```
- `reverse_right`  向右后方
- `forward_left`  向左前方
- `reverse_left`  向左后方


## reset
抬起所有的键
```
digitalWrite(right_pin, HIGH);
```

## send_command
执行命令

这里很清楚的看出**数字键和命令的关系**
```
case 0: reset(); break;
// 单命令
case 1: forward(time); break;
case 2: reverse(time); break;
case 3: right(time); break;
case 4: left(time); break;

//复合命令
case 6: forward_right(time); break;
case 7: forward_left(time); break;
case 8: reverse_right(time); break;
case 9: reverse_left(time); break;
```