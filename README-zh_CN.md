# 【指南】华硕路由器HND系列nvram分区说明，启发修改全区等（适用于ac86u,ax88u等新机型）


博通ARM64芯片的分区不同于之前ARMv7的分区，不能从分区名来看它的分区作用。


一般而言，博通ARM64的芯片CFE有三个部分，`cfe`、`cfe_ram`、和`cfe_rom`，至于三者的关系没去弄明白，反正就是都要有。


不过有一点是可以确定的,`mtd0`分区依然是`u-boot`所在的分区，但不是唯一的CFE分区，一般而言，完整的CFE有三个分区。


但是从研究的多个品牌的多台机器来看，`mtd0`分区被命名为`nvram`，实际上这个分区跟nvram没有任何关系，真正的nvram内容是由固件指定位置的，不同的厂家指定的位置和方式不同


首先说一下nvram内容对于博通芯片路由器的重要性：由于博通无线网卡需要nvram提供一些配置参数，所以如果错误的nvram内容将直接导致无线失效。


再说说华硕HND系列路由器（包含AX系列）将出厂nvram存放的位置及方式：


asuswrt将出厂`nvram`存在`misc1`分区中，此分区在固件中被定义为`mtd10`


asuswrt将实时的`nvram`存放在`misc2`中，此分区在固件中被定义为`mtd9`


## 接下来重点来了，修改`misc1`内容的步骤：

1. 通过`dd`命令将`mtd10`分区备份出来，命名为`misc1.img`

2. `misc1.img`是个ubifs格式的镜像，将其挂载可以得到里面有个`nvram.nvm`文件

3. `nvram.nvm`文件由`nvram.txt`文本文件转化而得，其转换命令为`nvserial -a -s 100 -o $(DFT_NVRAM_NAME)_nvram.nvm $(DFT_NVRAM_NAME)_nvram.txt`

4. 想要将`nvram.nvm`恢复到`nvram.txt`需要通过16进制字节流转换分隔符。

5. 修改`nvram.txt`后将其转换成`nvram.nvm`

6. 将修改后的`nvram.nvm`放置到一个ubifs文件系统中，将其打包成镜像

7. 在CFE 的miniweb中，选择上传`misc1`镜像，则出厂nvram修改完成（也可直接通过`dd`命令替换`mtd10`分区）


教程到此结束，这不是傻瓜化教程，只是给迷途大众一个方向，不要再用老机型的方式猜测怎么修改默认nvram，而且这个操作不是修改CFE，只是老机型(RT-AC68U等)的CFE分区明文包含了默认nvram数据而已，所以老有人将这个操作称之为改CFE。强调一遍，CFE是引导程序，不是你以为的文本数据！博通的新架构将默认nvram不再包含在CFE分区中，实则减少了很多动不动就喜欢刷CFE的傻缺的损失。


# Notes

## Languages
[Chinese 中文](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/blob/main/README-zh_CN.md)  |  [English](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/blob/main/README.md)  |  [Add more](https://github.com/JackMerlin/How-to-flash-the-CFE-of-RT-AC86U-and-other-HND-routers/new/main)

## Source
https://www.bigxd.com/2021/04/976.html



## License
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

[![CC BY-NC-SA 4.0](https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
