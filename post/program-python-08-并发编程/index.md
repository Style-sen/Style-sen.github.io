# 并发编程


## 线程

1. GIL锁，全称为Global Interpreter Lock，也就是全局解释器锁,在一个进程中每次只能有一个线程在运行。

### `threading`

### 线程间通讯

### 生产者和消费者问题

1. 使用全局变量。

### threadpool

#### 方法

```python
makeRequests(callable_, args_list, callback=None,exc_callback=_handle_thread_exception)#创建任务
```

#### 类

##### ThreadPool

```python
ThreadPool(n) # 创建一个线程池，线程个数为n
putRequest() # 将一个任务放进线程池中
poll() # 等待所有任务处理完
```

## 进程

