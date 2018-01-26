# Traffic_signal
作用是设置信号灯的模式


1. 设置红绿灯、
```python
const int r = 9;             //connect red led at pin 9    
const int y = 10;           //connect yellow led at pin 10
const int g = 11;           //connect green led at pin 11
const int sec = 1000;       //表示一秒 = 1000 毫秒
```

2. 初始化配置
```python
void setup() 
  {
    pinMode(r,OUTPUT);
    pinMode(y,OUTPUT);
    pinMode(g,OUTPUT);
    delay(sec);
  }
```

3. 进入循环

先让红灯亮 5 秒、熄灭，然后黄灯亮 5 秒、熄灭，然后 绿灯亮 5 秒、熄灭
```python
void loop()
    {
        digitalWrite(r,HIGH) ;
        delay(sec*5);
        digitalWrite(r,LOW) ;
        digitalWrite(y,HIGH) ;
        delay(sec*5);
        digitalWrite(y,LOW) ;
        digitalWrite(g,HIGH) ;
        delay(sec*5);
        digitalWrite(g,LOW) ;
        
    }
```