# **DISPLAY APP**开发指南

发布版本：1.0.0

作者邮箱：tony.zheng@rock-chips.com

日期：2019.09

文件密级：公开资料

---
**前言**

**概述**

**产品版本**

| **芯片名称**            | **RT Thread 版本** |
| ----------------------- | :---------------- |
| 全部支持 RT Thread 的芯片 |                   |

**读者对象**

本文档（本指南）主要适用于以下工程师：
技术支持工程师
软件开发工程师

**修订记录**

| **日期**   | **版本** | **作者** | **修改说明**                    |
| ---------- | -------- | -------- | ------------------------------- |
| 2019-09-04 | V1.0     | 郑永智  | 初始发布                        |

---

[TOC]

---


## 1 概述

Rockchip RT-Thread 显示应用基于 RT-Thread 显示驱动以及LittlevGL GUI实现时钟显示等界面。其内容包含图片、字符等显示，以及图片解压算法、旋转算法、多图层合成以及滚动效果等的使用。本文通过几个简单的用例，向开发者提供了关于Rockchip RT-Thread开发平台的显示应用参考。

## 2 基于 720x1280 屏幕的clock demo用例

### 2.1 代码路径
基于 720x1280 屏幕的clock demo用例代码路径为：

    .
    ├── applications
        └── clock

### 2.2 工程配置
通过以下简单配置开启该用例的配置：

**进入工程目录，执行menuconfig命令：**

    usr@host:~/rt-thread$ cd bsp/rockchip/rk2108/
    usr@host:~/rt-thread/bsp/rockchip/rk2108$ scons --menuconfig

**LittlevGL组件的配置：**

    Location:
      -> RT-Thread Components
          -> System
             -> LittlevGL2RTT: The LittlevGl gui lib adapter RT-Thread (RT_USING_LITTLEVGL2RTT [=y])
                -> LittlevGL2RTT Component Options

按照以下信息配置LittlevGL GUI:

![2-2-1-720_1280_clock_demo_lvgl_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/2-2-1-720_1280_clock_demo_lvgl_config.png)

**开发板配置**

    Location:
        -> RT-Thread board config

按照以下信息选择开发板配置：

![2-2-2-720_1280_clock_demo_board_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/2-2-2-720_1280_clock_demo_board_config.png)

**Pannel配置**

    Location:
        -> RT-Thread rockchip common drivers

按照以下配置，选择720x1280屏幕

![2-2-3-720_1280_clock_demo_pannel_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/2-2-3-720_1280_clock_demo_pannel_config.png)

**显示应用配置**

    Location:
        -> RT-Thread application

按照以下配置，打开“clock demo enable”配置

![2-2-4-720_1280_clock_demo_app_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/2-2-4-720_1280_clock_demo_app_config.png)


### 2.3 编译与执行

配置完成之后，在工程目录下执行编译命令：

    usr@host:~/rt-thread/bsp/rockchip/rk2108$ scons

编译完成之后将固件下载到开发板查看显示效果。

## 3 基于 240x320 屏幕的RK_IoT_Display显示用例

### 3.1 代码路径
基于 240x320 屏幕的RK_IoT_Display用例代码路径为：

    .
    ├── applications
        └── rk_iot_display

### 3.2 工程配置
通过以下简单配置开启该用例的配置：

**进入工程目录，执行menuconfig命令：**

    usr@host:~/rt-thread$ cd bsp/rockchip/rk2108/
    usr@host:~/rt-thread/bsp/rockchip/rk2108$ scons --menuconfig

**LittlevGL组件的配置：**

    Location:
      -> RT-Thread Components
          -> System
             -> LittlevGL2RTT: The LittlevGl gui lib adapter RT-Thread (RT_USING_LITTLEVGL2RTT [=y])
                -> LittlevGL2RTT Component Options

按照以下信息配置LittlevGL GUI:

![3-2-1-240_320_iot_diaplay_lvgl_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/3-2-1-240_320_iot_diaplay_lvgl_config.png)


**开发板配置**

    Location:
        -> RT-Thread board config

按照以下信息选择开发板配置：

![3-2-2-240_320_iot_diaplay_board_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/3-2-2-240_320_iot_diaplay_board_config.png)


**Pannel配置**

    Location:
        -> RT-Thread rockchip common drivers

按照以下配置，选择240x320屏幕

![3-2-3-240_320_iot_diaplay_pannel_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/3-2-3-240_320_iot_diaplay_pannel_config.png)

**显示应用配置**

    Location:
        -> RT-Thread application

按照以下配置，打开“IoT display enable”配置

![3-2-4-240_320_iot_diaplay_app_config.png](Rockchip_Developer_Guide_RT-Thread_Display_APP/3-2-4-240_320_iot_diaplay_app_config.png)


### 3.3 编译与执行

配置完成之后，在工程目录下执行编译命令：

    usr@host:~/rt-thread/bsp/rockchip/rk2108$ scons

编译完成之后将固件下载到开发板查看显示效果。

## 4 参考文档
1. [Rockchip_Developer_Guide_RT-Thread_Display_CN.md](../driver/display/Rockchip_Developer_Guide_RT-Thread_Display_CN.md)
2. 有关于LittlevGL GUI的说明文档及使用方法，参考网址：https://littlevgl.com/
