# 2019_USTC_Robogame
2019 USTC Robogame 永怀初心队(主要为程序设计部分)

## [规则及场地地图](Rules)

## 计划书

[计划书](plan/plan.doc)

## 原件选择及购买

### 单片机

正点原子STM32F103ZET6

据往年经验，一般有Arduino和STM32两种选择。Arduino对新手较为友好，上手较快。对很多功能，Arduino都有封装好的Api，而不需要自己实现。

PS：如果有再来一次的机会，我一定会选择Arduino，简单太多了。

另外，如果选择STM32。关于stm32的命名规则在[百度百科](<https://baike.baidu.com/item/stm32/9133302?fr=aladdin>) 中有介绍。一般F1系列就能满足要求，GPIO引脚一般用不完，我大概用了30多个GPIO。如果需要RFID的话每个RFID需要一个UART口。

建议购买相应的ST-Link或J-Link以用于在线调试。

### 轮

我们选择了麦克纳姆轮。

轮一般有普通轮（便宜），麦克纳姆轮，全向轮，履带等选择。

普通轮转弯需要差速转弯，麦克纳姆轮和全向轮可以原地转弯，履带越障较为方便。最后完成全部任务的选择的都是麦克纳姆轮或全向轮。但是全向轮上坡时控制难度比较高。

### 升降

本组搞机械的同学的创意：步进电机通过滑轮带动鱼线。网上有全套的升降设备，有一组花了八百。

![1572505264418](figs/1572505264418.png)

### 底盘

主要由工院同学完成。

### 机械爪

普通机械爪，也可用气泵带动吸盘。

## 电路

信号及控制线全部是杜邦线。吐槽杜邦线，的确太不稳定了，特别是越障之后经常掉线或者接触不良。且这类Debug时间太长了。

## 控制

[参考正点原子官方资料](<http://openedv.com/thread-13912-1-1.html>)

### PWM

参考正点原子官方例程，PWM输出实验。我需要用到10组PWM波输出，用了TIM1、2、3、4四个定时器输出。

PS：由于时间原因，而且需要用到的PWM波只需10组，我用的都是例程的方法输出PWM波，占用了4个定时器，而且每个定时器输出PWM波所用到的IO口时确定的， 较为不方便。

应该可以只用一个定时器作为计时器，输出任意多的PWM波。只需自己完成PWM函数就行了

### 轮的控制

麦克纳姆轮主要由单片机输出PWM波控制。

[电机驱动资料](motor driver)

电机驱动主要由5个口：VCC，GND，IN1,IN2,ENA。

IN1 ，IN2通过0，1控制正反转，停止，具体参考真值表。

ENA输入PWM波，通过控制占空比调节速度

**坑！！！！**

电机驱动板上VCC标为5V，所以我们当然将单片机的5V连给驱动板。但是。。。无论我们如何调节，都无法在IN1，IN2 都有输入的情况下控制电机正反转，必须有一个悬空轮才转。我们不可能通过插拔来控制正反转，难道要用一个舵机做一个插拔装置？？

花了三天时间：最后发现VCC给3.3V时才完全正常。悬空为1，所以之前错误的原因应该时高电平不够高。电表测为3.29V应该是高电平没有错。所以这个玄学原因至今没有确定。

### 巡线

主要是通过8个红外对管。主要思想就是往哪边偏哪边轮减速。

我们通过红外对管状态触发的状态机来确定小车的具体位置，事实证明这种方法鲁棒性太差。主要原因是红外对管的不稳定性。

红外对管在场地老旧时把灰尘识别为黑线，在越障时由于高度被抬高超过识别范围认为是车身偏了导致由黑变白，在日光较强时识别效果不好等等。。。所以除了巡线还是不要对红外对管有太多依赖，最好还是通过RFID识别场地上的标签来完成精准定位。

