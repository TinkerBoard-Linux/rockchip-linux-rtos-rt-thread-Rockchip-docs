# 音频开发指南

发布版本：V1.0.0

作者邮箱：jair.wu@rock-chips.com

日期：2019.09

文件密级：公开资料

---

**前言**

**概述**

本文档主要介绍RT-Thread音频开发的基本方法。

**产品版本**

| **芯片名称** | **内核版本**    |
| :----------- | --------------- |
| RK2108       | RT-Thread 3.1.3 |
|              |                 |

**读者对象**

本文档（本指南）主要适用于以下工程师：

技术支持工程师

软件开发工程师

**修订记录**

| **日期**   | **版本** | **作者** | **修改说明** |
| ---------- | -------- | -------- | ------------ |
| 2019-09-03 | V1.0.0   | 吴佳健   | 初始版本     |
|            |          |          |              |

---

[TOC]

---

## 1 文档及音频模块简介

RK2108上的音频相关模块有Analog MIC， PDM Digital MIC，VAD，AudioPWM和es8388 codec，其中es8388 codec同时具备录音、播放、回采功能。Analog MIC和PDM Digital MIC可以配合VAD使用。本文档主要介绍RK2108上音频模块的配置及简单测试命令的使用。

## 2 开发基础

### 2.1 音频配置

由于IO复用，部分音频通路间存在互斥关系，例如Analog MIC和PDM Digital MIC无法同时使用等。另由于VAD仅支持I2S0输入，因此在I2S1上的es8388 codec无法配合VAD使用。

#### 2.1.1 Analog MIC配置

在menuconfig中开启RT_USING_AUDIO_CARD_ACDCDIG和RT_USING_I2STDM0。

查看bsp/rockchip/rk2108/board/<board>[^1]/board.c的rk_board_audio_cards[]。其中VAD一项是需要使用VAD时才配置，并需要开启RT_USING_VAD。若要使用rt_device_read接口获取数据，则关闭RT_USING_VAD或设置`.vad = NULL`。

```c
{
    .name = "adc",
    .dai = I2STDM0,
#ifdef RT_USING_VAD
    .vad = VAD,
#endif
    .codec = ACDCDIG,
    .capture = true,
    .mclkfs = 2048,
    .format = AUDIO_FMT_I2S,
},
```

使用时对应声卡名为`adcc`。

注[^1]：<board>根据具体的板子修改对应的路径。

#### 2.1.2 PDM Digital MIC配置

在menuconfig中开启RT_USING_AUDIO_CARD_PDM_MIC和RT_USING_PDM0。PDM MIC无法与AMIC同时使用，需要关闭RT_USING_AUDIO_CARD_ACDCDIG。PDM MIC无法与LCDC同时使用，需要关闭RT_USING_VOP。

查看bsp/rockchip/rk2108/board/<board>/board.c的rk_board_audio_cards[]。其中VAD一项是需要使用VAD时才配置，并需要开启RT_USING_VAD。若要使用rt_device_read接口获取数据，则关闭RT_USING_VAD或设置`.vad = NULL`。

```c
{
    .name = "pdm",
    .dai = PDM0,
#ifdef RT_USING_VAD
    .vad = VAD,
#endif
    .codec = NULL,
    .capture = true,
    .format = AUDIO_FMT_PDM,
},
```

使用时对应声卡名为`pdmc`。

在使用过程中若发现PDM无数据，可使用io命令查看对应寄存器是否配置正确。具体寄存器请参考Rockchip RK2108 TRM Part1.pdf和Rockchip RK2108 TRM Part2.pdf对应章节。

#### 2.1.3 es8388 codec配置

在menuconfig中开启RT_USING_AUDIO_CARD_ES8388和RT_USING_I2STDM1。

对es8388 codec进行读写操作可以使用components/player/audio_sever/pcm.c中的接口，或使用rt_device相应接口直接操作。使用es8388 codec前需要调用`es8388_iomux_config()`配置相关iomux，该函数的具体实现在bsp/rockchip/rk2108/board/<board>/iomux.c中。

使用时对应声卡名为`es8388c`和`es8388p`，分别对应录音和播放。

可在bsp/rockchip/common/drivers/audio/codecs/es8388.c中调整es8388增益。其中0x1e为0dB，最小可设置为0x0，即-45dB，最大可设置为0x21，即4.5dB，每加1增加1.5dB。ES8388_DACCONTROL26为左声道，ES8388_DACCONTROL27为右声道。

```
/* set the LOUT2VOL and ROUT2VOL 0dB */
ret |= es_wr_reg(es8388->i2cdev, ES8388_DACCONTROL26, 0x1e);
ret |= es_wr_reg(es8388->i2cdev, ES8388_DACCONTROL27, 0x1e);
```

#### 2.1.4 Audio PWM配置

在menuconfig中打开RT_USING_AUDIO_CARD_AUDIOPWM，关闭RT_USING_VOP。

使用RK2108B_evb_v10板子时，使用Audio PWM需要将喇叭接至PWM out接口，并使用跳帽将PWM TRIODE引脚短接。

使用RK2108 AudioDemo板时，需要将喇叭接至核心板上SPEAKER接口。

修改bsp/rockchip/rk2108/board/<board>/iomux.c的rt_hw_iomux_config()函数，在其中添加`audio_iomux_config()`函数的调用。

使用时对应声卡名为`audpwmp`。

### 2.2 测试用例

需要在menuconfig中开启RT_USING_PLAYER。

#### 2.2.1 播放

需要在menuconfig中开启ENABLE_PLAYER_TEST。

播放测试函数的实现在components/player/audio_server/player_test.c，对应的测试命令为`player_test_start`与`player_test_stop`。

开始播放的命令为`player_test_start <filepath> [-D card] [-r resamplerate]`或`player_test_start loop [-D card] [-r resamplerate]`。`player_test_start <filepath>`会单曲循环播放指定路径的音频，`player_test_start loop`会循环播放`/music`目录下`0~4.mp3`五个文件，目前支持pcm、wav、amr、mp3，其中amr和mp3的播放需要在menuconfig中RT-Thread Components->Audio Codec项下开启对应解码器。参数-D可指定声卡播放，默认使用es8388[^2]，参数-r可指定重采样率，目前支持16KHz重采样，默认不重采样。使用`player_test_stop`停止播放。

注[^2]：具体是否能够使用某一声卡播放，需要使用list_device查看是否存在对应设备，录音设备以`c`结尾，放音设备以`p`结尾，另可查看bsp/rockchip/rk2108/board/<board>/board.c的rk_board_audio_cards[]中对应声卡的`.playback`是否开启。

#### 2.2.2 录音

需要在menuconfig中开启ENABLE_RECORDER_TEST。

录音测试函数的实现在components/player/audio_server/recorder_test.c，对应的测试命令为`record_start`与`record_stop`。

使用`record_start <filepath> [-D card] [-r samplerate] [-b bits] [-c channels]`开始录音，<filepath>为必选项，其余为可选项。-D为声卡，默认值为0；-r为采样率，默认值为16000；-b为位深，默认值为16；-c为声道数，默认值为2。使用`record_stop`停止录音。程序根据文件后缀名录制对应文件，目前支持“.pcm”、“.wav“文件。

### 2.3 Tinycap和Tinyplay

需要在menuconfig中开启RT_USING_COMMON_TEST_AUDIO。

函数的具体实现在bsp/rockchip/common/test目录下的tinycap.c和tinyplay.c。目前该命令支持wav文件的录放。

命令使用方法为`Tinycap <filepath> [-D card] [-r samplerate] [-b bits] [-c channels] [-p period_size] [-n n_periods] [-t seconds]`和`Tinyplay <filepath> [-D card] [-p period_size] [-n n_periods] [-t seconds]`。<filepath>为必选项，其余为可选项。-D为声卡，默认值为0；-r为采样率，默认值为16000；  -b为位深，默认值为16；-c为声道数，默认值为2；-p为DMA buffer的帧大小，默认为1024（bytes）；-n为DMA帧数，默认为4；-t为录音时长，默认为10（s）。

