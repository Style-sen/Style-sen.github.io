# STM32软件研发


## 1. 开始

1. 官方库的选择:使用Cube库替代官方外设库。

## 2. 资源

### 中断、事件、中断事件

1. 需触发中断，交给NVIC处理的，也就是软件上有中断函数的，叫中断
2. 不要触发中断，直接给外设(rg，ADC)的，软件上没有中断函数的，叫事件。
3. 触发中断，而且软件上也有中断函数的，叫中断事件
4. 事件机制提供了一个完全有硬件自动完成的触发到产生结果的通道,不要软件的参与,降低了CPU的负荷,节省了中断资源，提高了响应速度(硬件总快于软件)，是利用硬件来提升CPU芯片处理事件能力的一个有效方法。

### 2.1. 定时器

#### 2.1.1. PWM

1. 互补输出，应该是定时器的PWM互补输出。这个【作用】一般是用在驱动器、逆变器方面。主要是用在[【全桥MOS管】](https://blog.csdn.net/weixin_39922868/article/details/110714772)的控制上。
2. 如果MOS管相同，PWM不反向的话，势必造成两个MOS都导通。相当于电源和地都直连。
3. 由于两个MOS管的参数不一定完全相同，关断速度不一样，可能有那么几个us 或者MS 是上一个管马上要关闭，下一个已经打开了，造成了两个MOS都打开的短暂现象，所以一般需要加个死区时间。

#### HAL库

1. ` __HAL_TIM_SET_COMPARE()`设置CCRx，一般是用在PWM输出的，控制PWM占空比;第三个参数为占空比（高电平的重载值，值越大，高电平越多）。
2. `__HAL_TIM_GET_COMPARE()`用来读取CCRx的，一般用于捕获处理.
3. `HAL_TIM_PWM_Start()`开启PWM CH*
4. `HAL_TIM_PWM_Stop()`关闭PWM CH*
5. `HAL_TIMEx_PWM_Start()`开启PWM CH*N
6. `HAL_TIMEx_PWM_Stop()`关闭PWM CH*N

## 3. STM32CubeMX 工具的使用

### 3.1. 生成的文件夹结构

```shell
Drivers
    |——CMSIS #符合CMSIS标准的软件抽象层组件，包含两个文件夹
        |——Include #包含Cortex-M内核及其设备文件
        |——Device #微控制器专用头文件/启动代码/专用系统文件
    |——STM32F1xx_HAL_Driver # 包含了所有的STM32F1系列的HAL库头文件和源文件；屏蔽了底层的寄存器配置，统一外设接口。【不同的工程包含的HAL驱动文件是相同的】
Core
    |——Inc #应用相关的头文件；【不同的工程项目不同】
    |——Src #应用相关的源文件；【不同的项目不同】
MDK-ARM #MDK项目
    |
```

1. 如果需要重新生成代码，需要打开MDK工具，并最小化；生成代码之后，更新变化的文件。【切记：MDK工程文件不更新】
