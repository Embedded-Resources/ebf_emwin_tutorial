.. vim: syntax=rst

emWin文件和应用架构
------------

emWin的获取
~~~~~~~~

我们使用的平台是野火STM32开发板，所以我们使用STemWin版本的库，在本教程中我们使用的STemWin为V5.44a版本。

目前，ST官网中的STemWin版本停留在V5.32不再继续更新，ST已经将最新版本的STemWin放到了STM32 CUBE库中。STM32 CUBE库可以从官网或者STM32CubeMX软件中获得，也可以直接从野火F249挑战者V2的配套资料中得到。

emWin目录结构
~~~~~~~~~

解压下载得到的STM32 CUBE库后，进入到如下目录：

STM32Cube_FW_F4_V1.24.1\Middlewares\ST\STemWin

进入STemWin目录后见图 3‑1。

|Filest002|

图 3‑1 STemWin文件说明

STemWin库包含了如下内容：

-  Config文件夹：其中的GUIConf.c文件用于配置emWin的存储空间分配，GUIConf.h文件用于裁剪emWin的功能。其余的GUIDRV_Template.c、LCDConf_FlexColor.c、GUIDRV_wf.c是emWin应用在不同液晶时使用的不同驱动模板；

-  Documentation文件夹：它包含了《STemWin544.pdf》文件，是关于emWin库的用户手册，由于emWin库不是开源代码，所以在使用emWin的函数接口时，唯一可以参考的说明就是这个手册；

-  inc文件夹：它包含了全部的emWin库核心的头文件，各种关于库函数的声明、定义都在这些文件里面；

-  Lib文件夹：它包含了emWin的代码库文件，针对Cortex-M0、M3和M4这些不同 CPU内核以及是否优化，都有一个相对应的emWin库，在使用的时候我们要根据自己的平台选择合适的库文件；

-  OS文件夹：它包含了GUI_X.c及GUI_X_FreeRTOS.c文件，分别是对应裸机平台的GUI延时，和多任务系统平台的关于进程调度之类的一些emWin所需要的底层接口；

-  Simulation文件夹：它包含了用于PC端仿真时的lib库和一些依赖，但是不包含PC端仿真的完整工程。

-  Software文件夹：它包含了BmpCvt.exe、GUIBuilder.exe和FontCvt.exe 等PC端软件工具。BmpCvt.exe用于把图片数据转换成C文件，GUIBuilder.exe是一个使用图形界面方式开发emWin应用的工具(有点像Qt
  的图形开发方式)，而FontCvt.exe则用于字体文件转换；

-  Release_Notes.html文件：是关于emWin库不同发行版本的简要更新说明文档。

emWin文件说明
~~~~~~~~~

下面的针对具体的文件进行介绍，inc部分文件太多没有全部列出，见表格 3‑1。

表格 3‑1 emWin具体文件说明

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 目录/文件名                      | 说明
     - |

   * - Config
     - 液晶驱动接口、emWin配置文件      |

   * - GUIConf.c
     - 用于配置emWin使用的RAM空间大小   |

   * - GUIConf.h
     - 用于裁剪emWin的功能              |

   * - GUIDRV_Template.c
     - 应用在不同液晶时使用的驱动模板   |

   * - LCDConf_XXXX_Template.c
     -

   * - SIMconf.c
     - Windows模拟器配置文件            |

   * - Documentation
     - 包含emWin库函数说明文档          |

   * - STemWin544.pdf
     - 用户和参考指南                   |

   * - inc
     - emWin库核心的头文件              |

   * - XXXX.h
     - 各种关于库函数的声明、定义       |

   * - Lib
     - emWin的核心库文件                |

   * - MCD-ST Image SW LicenseAgreement V2.pdf
     - 软件许可协议                     |

   * - STemWin_CM4_wcXX.a
     - emWin核心库文件                  |  XX=16时适用于EWARM 7和Keil 5     |  XX=32时试用于EWARM 8和SW4STM32   |

   * - STemWin_CM4_wcXX_ot.a
     - ot表示此核心库已做优化           |

   * - STemWin_CM4_wcXX_ARGB.a
     - ARGB表示此核心库的逻辑           | 颜色从高位到低位的排列顺序为ARGB |

   * - STemWin_CM4_wcXX_ot_ARGB.a
     - 此库                             | 文件既做了优化又使用ARGB颜色排列 |

   * - STemWin_CM4_OS_wcXX.a
     - OS表示在操作                     | 系统环境下使用的核心库，其余同上 |

   * - STemWin_CM4_OS_wcXX_ot.a
     -

   * - STemWin_CM4_OS_wcXX_ARGB.a
     -

   * - STemWin_CM4_OS_wcXX_ot_ARGB.a
     -

   * - STemWin_WIN32.lib
     - 在Windows系统中仿真时用的核心库  |

   * - STemWin_WIN32_ARGB.lib
     -

   * - OS
     - emWin的时间相关文件              |

   * - GUI_X.c
     - 裸机环境下的延时和时基相关文件   |

   * - GUI_X_OS.c
     - 提供给操作系统的接口，用于在操   | 作系统环境下的延时和进程调度管理 |

   * - Software
     - 包含emWin的各种PC端软件工具      |

   * - JPEG2MovieScripts
     - 视频格式转换器用到的一些脚本文件 |

   * - Bin2C.exe
     - 二进制到C转换器                  |

   * - BmpCvtST.exe
     - 位图转换器                       |

   * - emVNC.exe
     - 通用VNC客户端                    |

   * - emWinPlayer.exe
     - 视频播放器                       |

   * - emWinSPY.exe
     - 运行时资源查看器                 |

   * - emWinView.exe
     - 模拟查看器                       |

   * - FontCvtST.exe
     - 字体生成器                       |

   * - GUIBuilder.exe
     - 界面编辑器                       |

   * - JPEG2Movie.exe
     - 视频格式转换器                   |

   * - License.txt
     - 软件许可协议                     |

   * - U2C.exe
     - UTF-8编码到C转换器               |

   * - Release_Notes.html
     - 关于emWin                        | 库不同发行版本的简要更新说明文档 |


STemWin V5.44a的库文件命名方式相比以前的版本有很大不同。除了依旧区分裸机平台和操作系统平台以外，V5.44a中的库文件全部变为.a格式，文件名中不再直接写出编译器的类型，而是使用wc16和wc32区分不同的编译器，标有wc16的可用于EWARM(IAR for ARM) 7和Keil
5，标有wc32的可用于EWARM(IAR for ARM) 8和SW4STM32(GCC)。同时，相比于5.2x版本，V5.44a版中还提供了官方优化过的库文件，ot就是优化的单词 optimization
的缩写。而文件名中的ARGB则对应了emWin从5.30开始的一项变化，这部分内容将在颜色章节进行讲解。

emWin应用的软件架构
~~~~~~~~~~~~

典型的emWin构建程序其架构见图 3‑2。

图 3‑2 emWin软件架构图

该架构看起来并不复杂，从最底层看起， 首先是硬件层，硬件层就是指最基本的板级支持软件包(BSP)，也就是我们常说的底层驱动，它提供了对硬件平台资源最直接的访问；emWin软件库承上启下，它运行在硬件层之上，与硬件层之间仅靠液晶驱动连接起来，而向应用层则提供了各种便于用户制作GUI应用的接口。

在没有使用emWin的软件架构中，很可能就是由硬件层直接过渡到应用层，甚至根本没有分层的概念，这样的应用程序将十分依赖于某个特定的硬件平台，不容易迁移到其它设备之中。从这个架构图可以看出，只需要编写好液晶驱动这个粘合emWin库和硬件BSP层的中间件，就可以正常使用emWin了，迁移应用到不同的平台
也简单得多。

.. |Filest002| image:: media\Filest002.png
   :width: 5.76806in
   :height: 1.99412in
