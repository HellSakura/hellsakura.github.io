---
title: 基于Arduino mini pro的蓝牙遥控小车制作（使用joystick控制）
date: 2021-03-05 02:31:08
updated: 2021-03-30 17:31:00
tags:
  - Arduino
categories:
  - Arduino
---
蓝牙遥控小车,基于Arduino mini pro，使用摇杆控制
<!--more-->
# 1、所需工具及组件
## 1.1工具列表

1. 电烙铁套装
1. 热熔胶
1. 万用表
1. 尖嘴钳
## 1.2组件列表
| 组件 | 型号 | 数量 | 备注 |
| --- | --- | :---: | --- |
| Ardunio mini pro | 5V/16M | 2 |  |
| L298N电机驱动模块 |  | 1 |  |
| 带底板HC-05主从机一体蓝牙模块 | HC05原装版 | 2 | 原装和非原装都可 |
| 游戏摇杆控制杆传感器 JoyStick模块 |  | 1 |  |
| 18650电池盒 | 2节带线 | 1 | 可买3节装 |
| 18650锂电池 3.7V | 2000-2100HAM | 2 | 建议买3节以得到11V电压 |
| USB转TTL CH340G模块 | CH340G | 1 |  |
| M3单头 六角铜柱 螺柱 | M3*20+6(10个） | 1 |  |
| M3单头 六角铜柱 螺柱 | 平头螺丝M3*5(100个） | 1 |  |
| M3单头 六角铜柱 螺柱 | 六角螺母M3 内径3MM(100个） | 1 |  |
| 杜邦线  一排40根 | 公对公（10CM) | 1 |  |
| 杜邦线  一排40根 | 公对母（10CM) | 1 |  |
| 杜邦线  一排40根 | 母对母（10CM) | 1 |  |
| 间距2.54MM单排排针  （20个） | 1*40PIN | 1 |  |
| Arduino MB102大面包板+电源模块 |  | 1 |  |
| 洞洞板 | 7*9cm | 1 |  |
| 微型小船形开关  | 黑色2脚2档铜脚（5只） | 1 |  |
| 智能小车底盘 强磁电机 |  | 1 | 两轮 |
| 小车底板 |  | 1 | 圆形（当作上盖，盖住接线） |
| 下方为购买后未用到的组件（目前未使用或使用中出现问题） |  |  |  |
| 18650锂电池充电板 | TYPE-C USB接口 | 1 |  |
|  HC-SR04 超声波传感器  |  | 1 |  |
| 3.7V转12V迷你DC-DC升压模块  |  | 1 | 输出电流不达标 |
| 蜂鸣器 | 3-~12V无源通用电磁式16欧2kHz（5只） | 1 |  |
| MT3608 2a升压板 DC-DC升压模块 |  | 1 |  |

## 1.3组件介绍
### 1.3.1 Ardunio mini pro
#### 特点

- [x] 体积小巧
- [x] IO齐全
- [ ] 外部引脚通孔焊接（需自行焊接）
- [ ] 直接下载程序（需自备USB转TTL串口线）
#### 示意图
![pro mini.jpg](/images/2021/03/05/1.jpeg)
买到的pro mini 应该是这种样子的，需要自行焊接


#### I/O图


![IO图.png](/images/2021/03/05/2.png)
**串行：0（RX）和1（TX）** 用于接收（RX）和发送（TX）TTL串行数据。**（一定要将其他设备的RXD、TXD分别接在pro mini 的TXD、RXD接口上，即发送对接收）**  

**PWM:** 3、5、6、9、10和11。 提供带[AnalogWrite](https://www.arduino.cc/en/Main/Reference/AnalogWrite) 功能的8位PWM输出。   
**RAW**用于为电路板提供原始电压  
**VCC**3.3V或5V电源
**GND**接地引脚
<div class="warning">

> 注意：如果使用5V电源供电则接VCC GND ，7~9V电源 RAW GND

</div>

> 你也可以选用Ardunio uno等其他型号



### 1.3.2 L298N电机驱动模块
#### 示意图
![L298N.jpg](/images/2021/03/05/3.jpeg)
#### 跳线帽使用说明
可以看到有3个跳线帽
**板载5V使能**、**通道A使能**和**通道B使能**
如果在**12V供电**处输入7~12V电压，可以安装上**板载5V使能**跳线帽，此时可以从**5V供电**处得到一个5V输出，可用于给pro mini 或其他组件供电。
当驱动电压高于12V,小于等于24V时，必须拔掉**板载5V使能**跳线帽，然后在**5V供电**端口外部接入5V电压对L298N内部逻辑电路供电，此时pro mini 等组件需外部单独供电。
可通过**通道A使能**及**通道B使能**这两个端口实现PWM调速（使用PWM调速时需取下跳线帽）
<div class="warning">

> 注意:L298N使用时需要与pro mini共地

</div>


### 1.3.3 HC-05蓝牙模块
网上售卖的分为兼容版和原装版两种，区别不是很大，我买的时候买了一个兼容板和原装板
#### 示意图
![HC-05兼容.jpg](/images/2021/03/05/4.jpeg)  
![HC-05原装.jpg](/images/2021/03/05/5.jpeg)
#### 接线方法
| USB转TTL | HC-05 |
| --- | --- |
| TXD | RXD |
| RXD | TXD |
| GND | GND |
| 5V | VCC |

注意：我这里兼容版是按照表格里的接线方法接线的，使用正常。使用原装版时，按照此方法电脑提示设备运转不正常，将USB转TTL的 VCC 对应了HC-05上的 VCC后，运作正常
### 1.3.4 USB转TTL CH340G模块
#### 示意图
![USB转TTL.jpg](/images/2021/03/05/6.jpeg)
#### 驱动
[CH341SER.ZIP](https://www.yuque.com/attachments/yuque/0/2020/zip/1370978/1596600916222-522166a5-306e-45a3-9acb-4ebfe147a5c0.zip?_lake_card=%7B%22uid%22%3A%221596600915956-0%22%2C%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2020%2Fzip%2F1370978%2F1596600916222-522166a5-306e-45a3-9acb-4ebfe147a5c0.zip%22%2C%22name%22%3A%22CH341SER.ZIP%22%2C%22size%22%3A202935%2C%22type%22%3A%22application%2Fx-zip-compressed%22%2C%22ext%22%3A%22zip%22%2C%22progress%22%3A%7B%22percent%22%3A99%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22id%22%3A%22jwf2P%22%2C%22card%22%3A%22file%22%7D)
### 1.3.5 JoyStick模块
#### 示意图
![joystick.jpg](/images/2021/03/05/7.jpeg)
#### 测试方法 (X接A0 Y接A1)
```c
#define joyX A0
#define joyY A1

int button=2;
int buttonState = 0;
int buttonState1 = 0;

void setup() {
  pinMode(7,OUTPUT);
  pinMode(button,INPUT);
  digitalWrite(button, HIGH);
  Serial.begin(9600);
}
void loop(void)
{
  Serial.print(analogRead(1));          //读取摇杆Y轴的值，串口显示
  Serial.print(",");
  Serial.print(analogRead(0));         //读取摇杆X轴的值，串口显示
  Serial.println(",");
  
  delay(1000);
}
```
打开串口监视器      可以看到随着摇杆的运动，数据在不断变换
![image.png](/images/2021/03/05/8.png)  
![image.png](/images/2021/03/05/9.png)
默认的位置在 513,525 左右，这是中间位置（不同摇杆这个数值可能不同）。
其值介于0到1023之间
### 1.3.6 小车底盘
将零件自行组装
![车1.jpg](/images/2021/03/05/10.jpeg)
![车2.jpg](/images/2021/03/05/11.jpeg)
请根据体感自行调整导柱长度，我的下方2个万向轮处给的8根导柱过长，导致小车不能直行，于是手动将其磨短了4mm左右。


# 2、程序设计及写入
IDE的使用为Arduino官方的IDE，下载地址[https://www.arduino.cc/en/Main/Software](https://www.arduino.cc/en/Main/Software)
## 2.1 遥控器程序
```c
int xAxis, yAxis;

void setup() {
  Serial.begin(38400); //蓝牙模块的默认通讯速率
}

void loop() {
  xAxis = analogRead(A0); //读取X轴数据
  yAxis = analogRead(A1); //读取Y轴数据
  
  //通过串口发送到从属HC-05蓝牙模块
  Serial.write(xAxis/4); //除以4以将数值从0-1023转换为0-256 （1字节）
  Serial.write(yAxis/4);
  delay(20);
}
```
通过将操纵杆X、Y轴的数值除以4，可以将其从0-1023转换为0-255之间的值，就可以通过蓝牙设备将其作为1个字节发送，这样易于从属设备接收。
## 2.2 小车控制程序

```c 
#define enA 10
#define in1 3
#define in2 5
#define enB 11
#define in3 6
#define in4 9

int xAxis, yAxis;
unsigned int  x = 0;
unsigned int  y = 0;

int motorSpeedA = 0;
int motorSpeedB = 0;

void setup() {
  pinMode(enA, OUTPUT);
  pinMode(enB, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);

  Serial.begin(38400); //蓝牙模块的默认通讯速率
}

void loop() {
  //默认值,摇杆在中心位置时不移动,依据前面摇杆测试自行填写,摇杆之间可能会有所不同
  x = 513 / 4;
  y = 525 / 4;

  //从蓝牙主设备读取数据,串行接收到2个字节的数据,即X轴、Y轴,使用 Serial.read 函数读取
  while (Serial.available() >= 2) { 
    x = Serial.read();
    delay(10);
    y = Serial.read();
  }
  delay(10);
  //将接收到的值从0-255转换成0-1023	
  xAxis = x*4;
  yAxis = y*4;
    
  //增加一点公差,并将470到550的值视为中心.如果向后移动摇杆的Y轴并且该值低于470,那么将两个电动机的旋转方向设置为向后,否则向前
  //Y轴用于前进及后退
  if (yAxis < 470) {
    //马达A前进
    digitalWrite(in1, HIGH);  //此处的 LOW 和 HIGH 请根据你的马达接线方式自行调整
    digitalWrite(in2, LOW);
    //马达B前进
    digitalWrite(in3, HIGH);
    digitalWrite(in4, LOW);
    //将Y轴的下降值从470转换为0,将PWM值从0转换为255,即电动机的速度
    motorSpeedA = map(yAxis, 470, 0, 0, 255);
    motorSpeedB = map(yAxis, 470, 0, 0, 255);
  }
  else if (yAxis > 550) {
    //马达A后退
    digitalWrite(in1, LOW);
    digitalWrite(in2, HIGH);
    //马达B后退
    digitalWrite(in3, LOW);
    digitalWrite(in4, HIGH);
    //将Y轴的上升值从550转换为1023,将PWM值从0转换为255,即电动机的速度
    motorSpeedA = map(yAxis, 550, 1023, 0, 255);
    motorSpeedB = map(yAxis, 550, 1023, 0, 255);
  }
  //如果摇杆在中心,则速度为0
  else {
    motorSpeedA = 0;
    motorSpeedB = 0;
  }

  //X轴用于左转及右转
  if (xAxis < 470) {
    //将X轴的下降值从470转换为0,将PWM值从0转换为255
    int xMapped = map(xAxis, 470, 0, 0, 255);
    //左转：降低左马达速度,增大右马达速度
    motorSpeedB = motorSpeedA + xMapped;
    motorSpeedA = motorSpeedB - xMapped;
    //将速度限制在0-255之间
    if (motorSpeedA < 0) {
      motorSpeedA = 0;
    }
    if (motorSpeedB > 255) {
      motorSpeedB = 255;
    }
  }
  if (xAxis > 550) {
    //将X轴的上升值从550转换为1023,将PWM值从0转换为255
    int xMapped = map(xAxis, 550, 1023, 0, 255);
    //右转：降低右马达速度,增大左马达速度
    motorSpeedB = motorSpeedA - xMapped;
    motorSpeedA = motorSpeedB + xMapped;
    //将速度限制在0-255之间
    if (motorSpeedA > 255) {
      motorSpeedA = 255;
    }
    if (motorSpeedB < 0) {
      motorSpeedB = 0;
    }
  }
  //根据施加的电压和电动机本身,在较低速度下,电动机将无法开始运行,并且会产生嗡嗡声,此处实际上是将速度限制在了75-255之间
  //PWM信号的值低于75，电动机将无法运动（可自行根据马达和电压不同调整）
  if (motorSpeedA < 75) {
    motorSpeedA = 0;
  }
  if (motorSpeedB < 75) {
    motorSpeedB = 0;
  }
  analogWrite(enA, motorSpeedA); //向马达A发送PWM信号
  analogWrite(enB, motorSpeedB); //向马达B发送PWM信号
}
```
## 2.3蓝牙模块的设置及配对
目前我们手里有两个HC-05蓝牙模块，我们需要将其中一个设置为主机，另一个设置为从机，来实现摇杆操控，为了实现这一点，我们需要在AT模式下使用AT命令。
在购买HC-05蓝牙模块时，卖家应该会发给你关于HC-05蓝牙模块的使用说明和蓝牙测试软件，如果没有，我这里有提供的材料
[驱动及测试工具.zip](https://www.yuque.com/attachments/yuque/0/2020/zip/1370978/1596595432493-03932491-976e-40f1-b40e-1f4fcd6ef151.zip?_lake_card=%7B%22uid%22%3A%221596595428917-0%22%2C%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2020%2Fzip%2F1370978%2F1596595432493-03932491-976e-40f1-b40e-1f4fcd6ef151.zip%22%2C%22name%22%3A%22%E9%A9%B1%E5%8A%A8%E5%8F%8A%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7.zip%22%2C%22size%22%3A4525096%2C%22type%22%3A%22application%2Fx-zip-compressed%22%2C%22ext%22%3A%22zip%22%2C%22progress%22%3A%7B%22percent%22%3A99%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22id%22%3A%22qoqha%22%2C%22card%22%3A%22file%22%7D)

1. 安装HC-05的驱动
1. 将蓝牙模块按照[接线图](#3、电路连接)与USB转TTL连接（[USB转TTL驱动](#驱动)）
1. 按住蓝牙模块上的小按钮，连接电脑，如果蓝牙模块指示灯大约每2秒闪烁一次，则表明我们已成功进入AT命令模式
1. 打开蓝牙测试软件，点击搜索端口，波特率38400，稍等一会就会显示端口（这里的端口可以在设备管理器查看，选择CH340对应的端口）![image.png](/images/2021/03/05/12.png)
1. 输入 AT 后敲一下回车，再点击发送命令，会回复 OK 代表我们已经在AT模式下了

![image.png](/images/2021/03/05/13.png)

6. 首先，我们需要将两个蓝牙模块的串口波特率更改为38400，出厂时自带应该是9600，然后记下你要作为从机的蓝牙模块的地址

HC-05蓝牙模块出厂默认就是从机模式，配对码为1234（配对码可自行更改，但要确保主机和从机一致，指令集在上方附件中有提供），在此处，我们只需要将其中一个改为主机模式，然后通过绑定地址的方式让主机自动连接到从机。
_以下为从机配置示例_
```c
AT
OK
AT+UART?  
+UART:9600,0,0             //表示当前波特率为9600
OK
AT+UART=38400,0,0,\r\n     //这里是兼容版的蓝牙模块,需要输入 \r\n 对于原装版的蓝牙模块不需要输入,直接输入 AT+UART=38400,0,0, 即可
OK
AT+UART?
+UART:38400,0,0
OK
AT+ROLE?                   //表示蓝牙模块处于从机模式
+ROLE:0
OK
AT+ADDR?
+ADDR:98d3:71:fdde39       //记下这个地址
OK
```
_以下为主机_配置示例
```c
AT
OK
AT+UART?  
+UART:9600,0,0
OK
AT+UART=38400,0,0,
OK
AT+UART?
+UART:38400,0,0
OK
AT+ROLE?
+ROLE:0
OK
AT+ROLE=1       //设置为主机模式
OK
AT+CMODE=0      //设置为固定地址连接模式
OK
AT+BIND=98d3,71,fdde39    //绑定之前的蓝牙地址,注意,在填写地址时,我们需要使用逗号而不是冒号
OK
```
这样，蓝牙模块的设置就完成了，断电后重新上电，两块蓝牙模块会自动配对连接
> 其他指令及详细说明请参阅上方附件中的HC-05指令集文档

## 2.4 程序写入

1. 将USB转TTL与 mini pro 连接（连接mini pro只有6个排针那一侧的接口）
| USB转TTL | mini pro |
| --- | --- |
| RXD | TXD |
| TXD | RXD |
| GND | GND |
| VCC | VCC |

2. 打开arduino的IDE，将上方程序填入，在工具栏中，选择 Arduino Pro or Pro mini，选择处理器，比如我买的这一块是5V/16M，选择端口，对应端口为CH340的端口，可以在设备管理器找到
2. 点击上传按钮，在下方状态栏从编译项目变成上传中，马上按下pro mini 上的红色复位键，这里要多次尝试确认时机，就会提示上传完成，这时程序就写入了pro mini 中了
2. 依次将小车和遥控器程序分别上传到两块pro mini 中

![image.png](/images/2021/03/05/14.png)
![image.png](/images/2021/03/05/15.png)
![image.png](/images/2021/03/05/16.png)
# 3、电路连接
## 3.1小车接线图
![小车接线图.png](/images/2021/03/05/17.png)


[小车接线图.pdf](https://www.yuque.com/attachments/yuque/0/2020/pdf/1370978/1596544534776-77141d11-35e4-4068-bfbe-ab23bfdbb2ee.pdf?_lake_card=%7B%22uid%22%3A%221596544532721-0%22%2C%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2020%2Fpdf%2F1370978%2F1596544534776-77141d11-35e4-4068-bfbe-ab23bfdbb2ee.pdf%22%2C%22name%22%3A%22%E5%B0%8F%E8%BD%A6%E6%8E%A5%E7%BA%BF%E5%9B%BE.pdf%22%2C%22size%22%3A713888%2C%22type%22%3A%22application%2Fpdf%22%2C%22ext%22%3A%22pdf%22%2C%22progress%22%3A%7B%22percent%22%3A99%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22id%22%3A%225o3vh%22%2C%22card%22%3A%22file%22%7D)
## 3.2遥控器接线图
![遥控器_bb.png](/images/2021/03/05/18.png)
[遥控器.pdf](https://www.yuque.com/attachments/yuque/0/2020/pdf/1370978/1596551583499-d7d64699-e098-404c-9f61-39b76eb2ad17.pdf?_lake_card=%7B%22uid%22%3A%221596551581102-0%22%2C%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2020%2Fpdf%2F1370978%2F1596551583499-d7d64699-e098-404c-9f61-39b76eb2ad17.pdf%22%2C%22name%22%3A%22%E9%81%A5%E6%8E%A7%E5%99%A8.pdf%22%2C%22size%22%3A837468%2C%22type%22%3A%22application%2Fpdf%22%2C%22ext%22%3A%22pdf%22%2C%22progress%22%3A%7B%22percent%22%3A99%7D%2C%22status%22%3A%22done%22%2C%22percent%22%3A0%2C%22id%22%3A%22ewMJs%22%2C%22card%22%3A%22file%22%7D)
# 4、安装
![成品1.jpg](/images/2021/03/05/19.jpeg)
![成品2.jpg](/images/2021/03/05/20.jpeg)

<div class="info">

> 实际中，我这里马达out3 和out4 接线交换了一下，即out3为黑线 out4 为橙线，若出现左右转相反请自行修改代码

</div>

# 5、总结
本来是打算全都用洞洞板连接，结果实际操作时发现不太好设计，于是就只是做了一个固定作用。其实可以不用洞洞板，只需要使用热熔胶固定即可，
图片左下方的是充电模块，因为一开始设计的时候，是打算两个电池并联，通过充电模块，再通过升压模块来给小车提供电源，但是实际使用时发现 `3.7V转12V迷你DC-DC升压模块` 出现问题，无法提供足量的电流，导致启动正常，电机运动会立刻断电
左侧的HC-SR04超声波模块 因为是发现升压模块出问题后重新修改的代码，并没有将其加入。
下方的是电源供给，将排针固定在了一小块洞洞板上，下方焊接在一起，接入电源的正负极即可。


至于遥控器，因为我没有找到合适的盒子来放置摇杆，目前是在面包板上使用，只是一个原型。




# Todo

- [x] 摇杆外壳
- [ ] OLED屏幕显示
- [ ] 更好的充电方式
- [ ] 全自动避障模式
- [ ] 声音控制



