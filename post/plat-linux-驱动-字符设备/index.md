# 驱动-字符设备


1. 字符设备驱动，需要支持 `O_NOBBLOCK`，默认是阻塞的。

## 1. 串口

1. linux下的串口或者usb转串口驱动都是依赖linux内核提供的tty核心、tty线路规划和tty驱动。

### 1.1. 调试串口

1. 查看某个串口的波特率等信息`stty -F /dev/ttyS0 -a  #ttyS0为要查看的串口`;
2. 设置串口参数`stty -F /dev/ttyS0 ispeed 115200 ospeed 115200 cs8`;
3. 打印串口数据`cat /dev/ttyS0 #串口传来数据就可以显示`;
4. 发送数据`echo "sss" > /dev/ttyS0`.

