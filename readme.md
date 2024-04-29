﻿# Q-Modbus组件包

## 1.简介

**Q-Modbus** 是一款快捷易用的modbus通信协议栈，支持RTU、TCP通信协议，支持主机、从机工作模块，支持多实例应用。采用通信链路与通信协议分离的设计架构，用户可灵活地搭配使用。

### 1.1目录结构

`Q-Modbus` 软件包目录结构如下所示：

``` 
modbus
├───inc                         // 头文件目录
│   |   modbus.h                // API接口头文件
│   |   modbus_backend.h        // 后端模块头文件
│   |   modbus_cfg.h            // 配置头文件
│   |   modbus_crc.h            // CRC校验模块头文件
│   |   modbus_cvt.h            // 数据转换模块头文件
│   |   modbus_pdu.h            // PDU模块头文件
│   |   modbus_port.h           // 移植接口头文件
│   |   modbus_rtu.h            // RTU通信协议模块头文件
│   |   modbus_tcp.h            // TCP通信协议模块头文件
│   └───typedef.h               // 数据类型定义头文件
├───src                         // 源码目录
│   |   modbus.c                // 主模块
│   |   modbus_backend.c        // 后端模块
│   |   modbus_crc.c            // CRC校验模块
│   |   modbus_cvt.c            // 数据转换模块
│   |   modbus_master.c         // 主机功能模块
│   |   modbus_pdu.c            // PDU模块
│   |   modbus_port_linux.c     // linux移植接口模块
│   |   modbus_port_rtt.c       // RT-Thread移植接口模块
│   |   modbus_port_slave.c     // 从机移植接口模块
│   |   modbus_rtu.c            // RTU通信协议模块
│   |   modbus_slave.c          // 从机功能模块
│   └───modbus_tcp.c            // TCP通信协议模块
├───sample                      // 使用示例目录
│   |   mb_sample_rtu_master.c  // RTU主机使用示例
│   |   mb_sample_rtu_slave.c   // RTU从机使用示例
│   |   mb_sample_tcp_master.c  // TCP主机使用示例
│   └───mb_sample_rtu_slave.c   // TCP从机使用示例
│   license                     // 软件包许可证
│   readme.md                   // 软件包使用说明
└───SConscript                  // RT-Thread 默认的构建脚本
```

### 1.2许可证

Q-Modbus package 遵循 LGPLv2.1 许可，详见 `LICENSE` 文件。

### 1.3依赖

- RT_Thread 4.0
- serial
- pin
- socket

## 2.使用

### 2.1接口函数说明

#### rs485_inst_t * rs485_create(char *serial, int baudrate, int parity, int pin, int level);
- 功能 ：动态创建rs485实例
- 参数 ：serial--串口设备名称
- 参数 ：baudrate--串口波特率
- 参数 ：parity--串口检验位
- 参数 ：pin--rs485收发模式控制引脚
- 参数 ：level--发送模式控制电平
- 返回 ：成功返回实例指针，失败返回NULL

#### int rs485_destory(rs485_inst_t * hinst);
- 功能 ：销毁rs485实例
- 参数 ：hinst--rs485实例指针
- 返回 ：0--成功,其它--失败

#### int rs485_config(rs485_inst_t * hinst, int baudrate, int databits, int parity, int stopbits);
- 功能 ：配置rs485通信参数
- 参数 ：hinst--rs485实例指针
- 参数 ：baudrate--通信波特率
- 参数 ：databits--数据位数, 5~8
- 参数 ：parity--检验位, 0~2, 0--无校验, 1--奇校验, 2--偶校验
- 参数 ：stopbits--停止位, 0~1, 0--1个停止位, 1--2个停止位
- 返回 ：0--成功，其它--错误

#### int rs485_set_recv_tmo(rs485_inst_t * hinst, int tmo_ms);
- 功能 ：设置rs485接收超时时间
- 参数 ：hinst--rs485实例指针
- 参数 ：tmo_ms--超时时间,单位ms
- 返回 ：0--成功，其它--错误

#### int rs485_set_byte_tmo(rs485_inst_t * hinst, int tmo_ms);
- 功能 ：设置rs485接收字节间隔超时时间
- 参数 ：hinst--rs485实例指针
- 参数 ：tmo_ms--超时时间,单位ms
- 返回 ：0--成功，其它--错误

#### int rs485_connect(rs485_inst_t * hinst);
- 功能 ：打开rs485连接
- 参数 ：hinst--rs485实例指针
- 返回 ：0--成功，其它--错误

#### int rs485_disconn(rs485_inst_t * hinst);
- 功能 ：关闭rs485连接
- 参数 ：hinst--rs485实例指针
- 返回 ：0--成功，其它--错误

#### int rs485_recv(rs485_inst_t * hinst, void *buf, int size);
- 功能 ：从rs485接收数据
- 参数 ：hinst--rs485实例指针
- 参数 ：buf--接收数据缓冲区指针
- 参数 ：size--缓冲区尺寸
- 返回 ：>=0--接收到的数据长度，<0--错误

#### int rs485_send(rs485_inst_t * hinst, void *buf, int size);
- 功能 ：向rs485发送数据
- 参数 ：hinst--rs485实例指针
- 参数 ：buf--发送数据缓冲区指针
- 参数 ：size--发送数据长度
- 返回 ：>=0--发送的数据长度，<0--错误

#### int rs485_break_recv(rs485_inst_t * hinst);
- 功能 ：中断rs485接收等待
- 参数 ：hinst--rs485实例指针
- 返回 ：0--成功，其它--错误

#### int rs485_send_then_recv(rs485_inst_t * hinst, void *send_buf, int send_len, void *recv_buf, int recv_size);
- 功能 ：先向rs485发送命令数据，然后从rs485接收响应数据，在多线程使用同一rs485时，可不受打扰地完成发送命令接收响应功能
- 参数 ：hinst--rs485实例指针
- 参数 ：send_buf--发送数据缓冲区指针
- 参数 ：send_len--发送数据长度
- 参数 ：recv_buf--接收数据缓冲区指针
- 参数 ：recv_size--接收缓冲区尺寸
- 返回 ：>=0--接收到的数据长度，<0--错误

### 2.2获取组件

- **方式1：**
通过 *Env配置工具* 或 *RT-Thread studio* 开启软件包，根据需要配置各项参数；配置路径为 *RT-Thread online packages -> peripherals packages -> rs485* 


### 2.3配置参数说明

| 参数宏 | 说明 |
| ---- | ---- |
| MB_USING_RAW_PRT		    | 使用原始通信数据打印
| MB_USING_ADDR_CHK         | 使用从机地址检查
| MB_USING_MBAP_CHK	        | 使用MBAP头检查
| MB_USING_PORT_RTT         | 使用rt-thread系统接口
| MB_USING_PORT_LINUX       | 使用linux系统接口
| MB_USING_RTU_BACKEND      | 使用RTU通信链路后端
| MB_USING_TCP_BACKEND      | 使用TCP通信链路后端
| MB_USING_RTU_PROTOCOL     | 使用MODBUS-RTU通信协议
| MB_USING_TCP_PROTOCOL     | 使用MODBUS-TCP通信协议
| MB_USING_MASTER           | 使用主机功能
| MB_USING_SLAVE            | 使用从机功能
| MB_USING_SAMPLE_RTU_MASTER| 使用基于RTU通信链路后端的主机示例
| MB_USING_SAMPLE_RTU_SLAVE | 使用基于RTU通信链路后端的从机示例
| MB_USING_SAMPLE_TCP_MASTER| 使用基于TCP通信链路后端的主机示例
| MB_USING_SAMPLE_TCP_SLAVE | 使用基于TCP通信链路后端的从机示例

## 3. 联系方式

* 维护：qiyongzhong
* 主页：https://github.com/qiyongzhong0/q-modbus
* 主页：https://gitee.com/qiyongzhong0/q-modbus
* 邮箱：917768104@qq.com
