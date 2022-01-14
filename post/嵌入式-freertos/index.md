# 嵌入式-FREERTOS


## 消息队列

1. FreeRTOS 的消息传递是数据的复制，而不是传递的数据地址。

### xQueueCreate

1. 函数 xQueueCreate 用于创建消息队列。
```
第 1 个参数是消息队列支持的消息个数。
第 2 个参数是每个消息的大小，单位字节。 
返回值， 如果创建成功会返回消息队列的句柄(xQueueHandle)，如果由于 FreeRTOSConfig.h 文件中 heap 大小不足，无法为此消息队列提供所需的空间会返回 NULL。
```

### xQueueSend

1. 函数 xQueueSend 用于任务中消息发送
```
第 1 个参数是消息队列句柄。
第 2 个参数要传递数据地址， 每次发送都是将消息队列创建函数 xQueueCreate 所指定的单个消息大小复制到消息队列空间中。
第 3 个参数是当消息队列已经满时，等待消息队列有空间时的最大等待时间，单位系统时钟节拍。
返回值，如果消息成功发送返回 pdTRUE，否则返回 errQUEUE_FULL。
```
2. 此函数是用于任务代码中调用的，故不可以在中断服务程序中调用此函数，中断服务程序中使用的是xQueueSendFromISR.
3. 消息队列还有两个函数 xQueueSendToBack 和 xQueueSendToFront，函数 xQueueSendToBack实现的是 FIFO 方式的存取，函数 xQueueSendToFront 实现的是 LIFO 方式的读写。我们这里说的函数 xQueueSend 等效于 xQueueSendToBack，即实现的是 FIFO 方式的存取.
4. 消息队列还有两个函数 xQueueSendToBackFromISR 和 xQueueSendToFrontFromISR，函数xQueueSendToBackFromISR 实现的是 FIFO 方式的存取，函数 xQueueSendToFrontFromISR 实现的是 LIFO 方式的读写。我们这里说的函数 xQueueSendFromISR 等效于xQueueSendToBackFromISR，即实现的是 FIFO 方式的存取.

### xQueueReceive
1. 函数 xQueueReceive 用于接收消息队列中的数据。
```
第 1 个参数是消息队列句柄。
第 2 个参数是从消息队列中复制出数据后所储存的缓冲地址，缓冲区空间要大于等于消息队列创建函数 xQueueCreate 所指定的单个消息大小，否则取出的数据无法全部存储到缓冲区，从而造成内存溢出。
第 3 个参数是消息队列为空时，等待消息队列有数据的最大等待时间，单位系统时钟节拍。
返回值，如果接到到消息返回 pdTRUE，否则返回 pdFALSE。
```

## 信号量

参考[FreeRTOS 信号量](https://www.jianshu.com/p/2fbeb28c7fcd)

1. FreeRTOS 信号量和互斥锁是基于队列实现的；
2. FreeRTOS 信号量包括二进制信号量、计数信号量、互斥锁和递归互斥锁。

### 二进制信号量

1. 可以用于互斥和同步， 多用于同步。
```
xSemaphoreCreateBinary()    // 创建二进制信号量
xSemaphoreTake( xSemaphore, xBlockTime )    // 获取信号量 信号量变为0
xSemaphoreTakeFromISR( xSemaphore, pxHigherPriorityTaskWoken )    // 在中断中获取信号量  信号量变为0
xSemaphoreGiveFromISR( xSemaphore, pxHigherPriorityTaskWoken )    // 在中断中释放信号量  信号量变为1
xSemaphoreGive( xSemaphore )    // 任务中释放信号量  信号量变为1
```

### 计数信号量

1. 二进制信号量是长度为1的队列， 计数信号量则是长度可以大于1的信号量， 当设置长度为1， 其行为和二进制型号量一样。
```
xSemaphoreCreateCounting( uxMaxCount, uxInitialCount )    // 计数信号量创建时需要设置两个参数，一个是最大的计数值， 另一个初始化计数值
其他同二进制信号量
```

### 互斥锁

1. 当一个任务访问一个资源时， 需要获取令牌， 在其使用期间，其他任务不能使用该资源， 使用完后， 释放令牌， 其他任务可以访问， 保证资源在一段时间只能由一个任务读取修改。
2. 与二进制信号量最大的不同在于， 互斥信号量带有优先级继承的机制，这个机制用于减低优先级反转的影响。
3. 互斥锁不能在中断使用， 因为中断函数没有优先级继承，同时， 中断函数不能阻塞。

```
xSemaphoreCreateMutex()    // 创建互斥锁
xSemaphoreTake( xSemaphore, xBlockTime )    // 拿锁
xSemaphoreGive( xSemaphore )    // 放锁
```

### 递归互斥锁

1. 获取递归互斥量的任务可以重复获取该递归互斥量。
2. 使用xSemaphoreTakeRecursive() 函数成功获取几次递归互斥量，对应的就要使用xSemaphoreGiveRecursive()函数返还几次，在此之前递归互斥量都处于无效状态， 其他任务无法获取， 必须等待获取的任务释放完毕。
```
xSemaphoreCreateRecursiveMutex()    // 创建
xSemaphoreTakeRecursive( xMutex, xBlockTime )     // 获取递归互斥量
xSemaphoreGiveRecursive( xMutex )    // 释放互斥锁
```

## 事件标志祖

信号量能同步1对1，事件标志组可以同步多对1，并且可以选择是否请掉标志。

### API函数

```
EventGroupHandle_t xEventGroupCreate( void );    // 创建消息标志组，如果创建成功，此函数返回事件标志组的句柄
xEventGroupCreateStatic()
vEventGroupDelete()
EventBits_t xEventGroupWaitBits(
                       const EventGroupHandle_t xEventGroup,  /* 事件标志组句柄 */
                       const EventBits_t uxBitsToWaitFor,     /* 等待被设置的事件标志位 */
                       const BaseType_t xClearOnExit,         /* 选择是否清零被置位的事件标志位 */
                       const BaseType_t xWaitForAllBits,      /* 选择是否等待所有标志位都被设置 */
                       TickType_t xTicksToWait );             /* 设置等待时间 */
                       // 返回值，由于设置的时间超时或者指定的事件标志位被置1，导致函数退出时返回的事件标志组数值。
EventBits_t xEventGroupSetBits( EventGroupHandle_t xEventGroup,  /* 事件标志组句柄 */
                                const EventBits_t uxBitsToSet ); /* 事件标志位设置 */
                                // 返回当前的事件标志组数值。用户通过参数uxBitsToSet设置的标志位并不一定会保留到此函数的返回值中
BaseType_t xEventGroupSetBitsFromISR(
                          EventGroupHandle_t xEventGroup,          /* 事件标志组句柄 */
                          const EventBits_t uxBitsToSet,           /* 事件标志位设置 */
                          BaseType_t *pxHigherPriorityTaskWoken ); /* 高优先级任务是否被唤醒的状态保存 */
                          // 
xEventGroupClearBits()
xEventGroupClearBitsFromISR()
xEventGroupGetBits()
xEventGroupGetBitsFromISR()
xEventGroupSync()
```
