.. vim: syntax=rst

移植emWin到STM32—8080并口
--------------------

要使emWin在特定的硬件平台上运行，需要为emWin提供与底层硬件操作相关的函数接口，主要为内存管理接口、液晶驱动接口和触摸屏接口。

本章以配套例程“FreeRTOS_STemWIN544_移植模版”为例，对emWin的执行过程和移植步骤进行解构，阅读时请打开该工程配套学习。

本章第1、2、3小节适用于野火STM32全系列开发板，第4、5、6小节适用于显示接口为8080并口（mini、指南者、霸道、霸天虎）的开发板，适用于RGB接口开发板（F4\F7\H7挑战者、H743\H750 Pro）的移植过程单独放到下一章。

emWin的初始化
~~~~~~~~~

在移植emWin之前，我们需要了解它的初始化过程以及它依赖的硬件接口。

相信读者已经通过上一章的emWin仿真教程章节知道，在使用emWin前，都要先调用GUI_Init函数初始化emWin运行所需要的环境，在这个GUI_Init函数里，调用了emWin底层的配置函数，见图 5‑1。它按顺序分别调用了GUI_X_Config、LCD_X_Config及LCD_X_Dis
playDriver函数，完成了存储器分配、屏幕类型配置及初始化液晶驱动的工作。这些函数在前面的emWin仿真章节已经简要介绍过，在Win32仿真环境下的这些函数，已由Segger官方配置过，我们无需修改也能正常运行，而在嵌入式平台上，就必须修改这几个函数，以适应特定平台的需求。

|transp002|

图 5‑1 emWin初始化流程

除了上述函数的修改，还有一些emWin配置文件和操作系统接口相关文件的修改，综合各文件和配置的修改情况，可整理出移植emWin所需要的配置：

-  配置系统功能；

-  配置操作系统接口；

-  配置内存管理接口；

-  配置显示驱动接口；

-  配置触摸屏接口。

配置系统功能
~~~~~~

在emWin启动前需要对其功能进行适当的配置，它们的代码位于GUIConf.h文件中，见代码清单 5‑1。

代码清单 5‑1 GUIConf.h文件内容

1 #ifndef GUICONF_H

2 #define GUICONF_H

3

4 /\*

5 \*

6 \* Multi layer/display support

7 \*/

8 #define GUI_NUM_LAYERS 2 // Maximum number of available (1)

9 layers

10

11 /\*

12 \*

13 \* Multi tasking support

14 \*/

15 #ifdef OS_SUPPORT

16 #define GUI_OS (1) // Compile with multitasking (2)

17 support

18 #else

19 #define GUI_OS (0)

20 #endif

21

22 /\*

23 \*

24 \* Configuration of touch support

25 \*/

26 #ifndef GUI_SUPPORT_TOUCH

27 #define GUI_SUPPORT_TOUCH (1) // Support touchscreen (3)

28 #endif

29

30 /\*

31 \*

32 \* Default font

33 \*/

34 #define GUI_DEFAULT_FONT &GUI_Font6x8 (4)

35

36 /\*

37 \*

38 \* Configuration of available packages

39 \*/

40 #define GUI_SUPPORT_MOUSE (1) /\* Support a mouse \*/ (5)

41 #define GUI_WINSUPPORT (1) /\* Use window manager \*/ (6)

42 #define GUI_SUPPORT_MEMDEV (1) /\* Memory device package (7)

43 available \*/

44 #define GUI_SUPPORT_DEVICES (1) /\* Enable use of device (8)

45 pointers \*/

46

47 /\*

48 \*

49 \* External memory support

50 \*/

51 #define USE_EXTMEMHEAP (1) (9)

52

53 /\*

54 \*

55 \* ARGB support

56 \*/

57 #define GUI_USE_ARGB (1) (10)

58

59 #endif /\* Avoid multiple inclusion \*/

(1) GUI_NUM_LAYERS设置了emWin最大支持的显示图层数。其实GUIConf.h文件中的此参数是无效的，对于8080并口的开发板，由于芯片本身性能的限制不能很好的支持多图层显示；对于RGB接口的开发板，在配置显示驱动接口的时候会对这个宏进行重定义，真正起作用的是重定义之后的宏；

(2) GUI_OS定义了是否支持操作系统和多任务功能，为1时支持，可通过OS_SUPPORT宏来设置，在教程配套的例程中我们将OS_SUPPORT宏定义放在了MDK的预编译选项中，见图 5‑2；

|transp003|

图 5‑2 OS_SUPPORT宏的定义位置

(3) GUI_SUPPORT_TOUCH设置是否支持触摸操作，为1时支持；

(4) GUI_DEFAULT_FONT设置默认字体大小，这个宏一般不会修改；

(5) GUI_SUPPORT_MOUSE设置是否启用鼠标；

(6) GUI_WINSUPPORT设置是否使用窗口管理器；

(7) GUI_SUPPORT_MEMDEV设置是否使用内存设备；

(8) GUI_SUPPORT_DEVICES设置是否使用设备指针；

(1)

(2)

(3)

(4)

(5)

(6)

(7)

(8)

(9)  USE_EXTMEMHEAP是自行添加的宏，设置是否使用外部SDRAM作为emWin的内存堆，为1时使用外部SDRAM，为0时使用内部RAM，作用于GUIConf.c文件；

(10) GUI_USE_ARGB设置emWin运行时的逻辑颜色格式，虽然emWin V5.30以后支持ARGB颜色格式，但默认格式仍然是ABGR，所以需要使用该宏进行切换。对于野火F1系列开发板，只要使用名字带ARGB的emwin库即可，此宏不用设置。

配置操作系统接口
~~~~~~~~

使用操作系统时，需要给emWin提供操作系统的接口，emWin的内核使用这些接口管理系统资源，便于适配不同的操作系统。以本教程配套的FreeRTOS系统为例，emWin对于该操作系统接口的示例文件为GUI_X_FreeRTOS.c，见代码清单 5‑2。

代码清单 5‑2 GUI_X_FreeRTOS.c内容

1 /\* Includes
--------------

2 -------*/

3

4 #include "GUI.h"

5

6 /\* FreeRTOS头文件 \*/

7 #include "FreeRTOS.h"

8 #include "task.h"

9 #include "semphr.h"

10 /\*

11 \*

12 \* Global data

13 \*/

14 static xSemaphoreHandle xQueueMutex = NULL;

15 static xSemaphoreHandle xSemaTxDone = NULL;

16

17 /\*

18 \*

19 \* Timing:

20 \* GUI_X_GetTime()

21 \* GUI_X_Delay(int)

22

23 Some timing dependent routines require a GetTime

24 and delay function.
Default time unit (tick), normally is

25 1 ms.

26 \*/

27 int GUI_X_GetTime(void)

28 {

29 return ((int) xTaskGetTickCount());

30 }

31

32 void GUI_X_Delay(int ms)

33 {

34 vTaskDelay( ms );

35 }

36

37 /\*

38 \*

39 \* GUI_X_Init()

40 \*

41 \* Note:

42 \* GUI_X_Init() is called from GUI_Init is a possibility to init

43 \* some hardware which needs to be up and running before the GUI.

44 \* If not required, leave this routine blank.

45 \*/

46

47 void GUI_X_Init(void)

48 {

49 }

50

51

52 /\*

53 \*

54 \* GUI_X_ExecIdle

55 \*

56 \* Note:

57 \* Called if WM is in idle state

58 \*/

59

60 void GUI_X_ExecIdle(void)

61 {

62 GUI_X_Delay(1);

63 }

64

65 /\*

66 \*

67 \* Multitasking:

68 \*

69 \* GUI_X_InitOS()

70 \* GUI_X_GetTaskId()

71 \* GUI_X_Lock()

72 \* GUI_X_Unlock()

73 \*

74 \* Note:

75 \* The following routines are required only if emWin is used in a

76 \* true multi task environment, which means you have more than one

77 \* thread using the emWin API.

78 \* In this case the

79 \* #define GUI_OS 1

80 \* needs to be in GUIConf.h

81 \*/

82

83 /\* Init OS \*/

84 void GUI_X_InitOS(void)

85 {

86 /\* 创建互斥信号量 用于资源共享 \*/

87 xQueueMutex = xSemaphoreCreateMutex();

88 configASSERT (xQueueMutex != NULL);

89 /\* 创建二值信号量 用于事件触发 \*/

90 vSemaphoreCreateBinary( xSemaTxDone );

91 configASSERT ( xSemaTxDone != NULL );

92 }

93

94 void GUI_X_Unlock(void)

95 {

96 /\* 给出互斥量 \*/

97 xSemaphoreGive(xQueueMutex);

98 }

99

100 void GUI_X_Lock(void)

101 {

102 if (xQueueMutex == NULL) {

103 GUI_X_InitOS();

104 }

105 /\* 获取互斥量 \*/

106 xSemaphoreTake(xQueueMutex, /\* 互斥量句柄 \*/

107 portMAX_DELAY);/\* 阻塞等待 \*/

108 }

109

110 /\* Get Task handle \*/

111 U32 GUI_X_GetTaskId(void)

112 {

113 return ((U32) xTaskGetCurrentTaskHandle());

114 }

115

116

117 void GUI_X_WaitEvent (void)

118 {

119 /\* 获取信号量 \*/

120 while (xSemaphoreTake(xSemaTxDone, /\* 信号量句柄 \*/

121 portMAX_DELAY) != pdTRUE);/\* 阻塞等待 \*/

122 }

123

124

125 void GUI_X_SignalEvent (void)

126 {

127 /\* 给出信号量 \*/

128 xSemaphoreGive(xSemaTxDone);

129 }

130

131 /\*

132 \*

133 \* Logging: OS dependent

134

135 Note:

136 Logging is used in higher debug levels only.
The typical target

137 build does not use logging and does therefor not require any of

138 the logging routines below.
For a release build without logging

139 the routines below may be eliminated to save some space.

140 (If the linker is not function aware and eliminates unreferenced

141 functions automatically)

142

143 \*/

144

145 void GUI_X_Log (const char \*s) { }

146 void GUI_X_Warn (const char \*s) { }

147 void GUI_X_ErrorOut(const char \*s) { }

148

149 /\* End of file \/

150

上述示例代码就是针对具体的操作系统进行封装，它封装的操作系统接口总结如表格 5‑1所示，主要包括互斥信号量和二值信号量的创建、获取和释放的操作，还包含有获取任务句柄、当前系统时间戳以及延时相关的操作。

表格 5‑1 需要提供给emWin的操作系统接口

================= ==============================
操作系统接口      功能
================= ==============================
GUI_X_GetTime     获取当前系统时间（单位：毫秒）
GUI_X_Delay       延时函数（单位：毫秒）
GUI_X_ExecIdle    窗口管理器空闲时调用
GUI_X_InitOS      创建互斥信号量和二值信号量
GUI_X_Unlock      互斥解锁
GUI_X_Lock        互斥锁定
GUI_X_GetTaskId   获取当前任务句柄
GUI_X_WaitEvent   等待二值信号量
GUI_X_SignalEvent 发送二值信号量
================= ==============================

GUI_X_FreeRTOS.c中除了有操作系统接口之外，还有一些调试日志和错误输出接口，不过几乎很少会用到，在此不做介绍。

配置内存管理接口
~~~~~~~~

从这一小节开始，8080并口和RGB接口的移植步骤就不一样了，RGB接口的移植请直接跳到下一章。

emWin启动时，GUI_X_Config函数是GUI_Init调用的第一个初始化函数，这个函数初始化了emWin的运行内存，代码位于GUIConf.c文件中，见代码清单 5‑3。

代码清单 5‑3 GUIConf.c文件内容

1 #include "GUI.h"

2 #include "./sram/bsp_fsmc_sram.h"

3

4 /\*

5 \*

6 \* Defines, configurable

7 \*

8 \\*

9 \*/

10 /\* 定义用于GUI的可用字节数 \*/

11 #if USE_EXTMEMHEAP

12 #define GUI_NUMBYTES (1024 \* 1024) // xByte (1)

13 #else

14 #define GUI_NUMBYTES (1024 \* 32) // xByte

15 #endif

16

17 /\*

18 \*

19 \* Static data

20 \*

21 \\*

22 \*/

23 #if USE_EXTMEMHEAP (2)

24 static U32 HeapMem[GUI_NUMBYTES / 4] \__attribute__((at(

25 Bank1_SRAM3_ADDR)));

26 #else

27 static U32 extMem[GUI_NUMBYTES / 4];

28 #endif

29

30 /\*

31 \*

32 \* Public code

33 \*

34 \\*

35 \*/

36 /\*

37 \*

38 \* GUI_X_Config

39 \*

40 \* Purpose:

41 \* Called during the initialization process in order to set up the

42 \* available memory for the GUI.

43 \*/

44 void GUI_X_Config(void)

45 {

46 #if USE_EXTMEMHEAP

47 GUI_ALLOC_AssignMemory(HeapMem, GUI_NUMBYTES); (3)

48 #else

49 GUI_ALLOC_AssignMemory(extMem, GUI_NUMBYTES);

50 #endif

51 }

上述代码中的第1部分定义了emWin所使用内存堆大小的宏GUI_NUMBYTES，第2部分是利用前1个宏定义了一个静态的，大小为GUI_NUMBYTES / 4的数组HeapMem，并将数组的首地址定位到Bank1_SRAM3_ADDR处，第3部分利用库函数GUI_ALLOC_AssignMemor
y把该静态数组分配给emWin作为内存块使用。旧版例程中还用到了GUI_ALLOC_SetAvBlockSize函数配置每个小存储块的平均大小，但在本教程使用的V5.44a版本已经不再需要GUI_ALLOC_SetAvBlockSiz函数。

用户可通过GUI_NUMBYTES改变emWin使用的内存块大小，在本示例中的1024KB。也可通过GUIConf.h中的USE_EXTMEMHEAP宏来选择emWin的内存堆是在内部RAM还是外部SRAM。

请注意，应用程序无法使用全部的内存空间，因为emWin的内存管理系统本身使用了其中很小的一部分，大约12个字节用于系统管理。

配置显示驱动接口
~~~~~~~~

emWin的显示驱动接口主要包括在LCDConf_FlexColor.c文件中，移植时主要针对LCDConf_FlexColor.c文件进行修改，同时也是emWin移植的重点对象。

LCD_X_Config函数
^^^^^^^^^^^^^^

先来了解显示驱动函数LCD_X_Config的程序流程，它会在GUI_X_Config函数结束后被调用，具体见代码清单 5‑4。

代码清单 5‑4 LCD_X_Config接口（LCDConf_FlexColor.c文件）

1 void LCD_X_Config(void)

2 {

3 GUI_DEVICE \* pDevice;

4 CONFIG_FLEXCOLOR Config = {0};

5 GUI_PORT_API PortAPI = {0};

6

7 /\* 第1部分，链接显示驱动和颜色转换程序 \*/

8 pDevice = GUI_DEVICE_CreateAndLink(GUIDRV_FLEXCOLOR, GUICC_M565, 0,

9 0);

10 /\* 设置显示区域尺寸 \*/

11 LCD_SetSizeEx (0, XSIZE_PHYS , YSIZE_PHYS);

12 LCD_SetVSizeEx(0, VXSIZE_PHYS, VYSIZE_PHYS);

13

14 /\* 第2部分，设置液晶驱动基础选项 \*/

15 Config.FirstCOM = 0; //modify by fire

16 Config.FirstSEG = 0; //modify by fire

17 Config.Orientation = GUI_MIRROR_Y|GUI_MIRROR_X;//modify by fire

18 竖屏

19 Config.NumDummyReads = 2; //modify by fire

20 读取的第二个数据才是真实数据

21

22 GUIDRV_FlexColor_Config(pDevice, &Config);

23

24 /\* 第3部分，添加液晶读写函数 \*/

25 PortAPI.pfWrite16_A0 = LcdWriteReg;

26 PortAPI.pfWrite16_A1 = LcdWriteData;

27 PortAPI.pfWriteM16_A1 = LcdWriteDataMultiple;

28 PortAPI.pfReadM16_A1 = LcdReadDataMultiple;

29 GUIDRV_FlexColor_SetFunc(pDevice, &PortAPI,

30 GUIDRV_FLEXCOLOR_F66709,

31 GUIDRV_FLEXCOLOR_M16C0B16);//modify by

32 fire

33 }

LCD_X_Config函数的作用是创建一个显示驱动器件，设置颜色转换程序，并配置物理显示尺寸。如代码清单 5‑4所示，此函数可分为3个部分，每个部分各用到了1个配置结构体，共使用了3个。

GUI_DEVICE类型
''''''''''''

LCD_X_Config函数在创建显示驱动器件时，使用了GUI_DEVICE类型的变量来记录液晶驱动器的4种属性，关于它的定义见代码清单 5‑5。

代码清单 5‑5 GUI_DEVICE原型

1 struct GUI_DEVICE {

2 /\* Linking \*/

3 GUI_DEVICE \* pNext;

4 GUI_DEVICE \* pPrev;

5 /\* Data \*/

6 union {

7 GUI_HMEM hContext; // Handle of payload data like sprite- or memory device context

8 void \* pContext; // Pointer for context data in a fixed block

9 } u;

10 /\* API pointers \*/

11 const GUI_DEVICE_API \* pDeviceAPI;

12 const LCD_API_COLOR_CONV \* pColorConvAPI;

13 U16 Flags;

14 int LayerIndex;

15 };

1) GUI_DEVICE_API：用于记录液晶的驱动类型，它指向显示驱动的指针。emWin为了兼容不同的液晶驱动器，分类设置了多种驱动方案供用户选择，如GUIDRV_BitPlains、GUIDRV_Dist、GUIDRV_FlexColor和GUIDRV_Lin等，本章配套的移植模版使用的液晶驱
动器是ILI9341，通过在《STemWin5.44参考指南》中查找，得到ILI9341在emWin中使用的是GUIDRV_FlexColor类型驱动，见图 5‑3。

|transp004|

图 5‑3 emWin对液晶驱动器ILI9341的支持

2) LCD_API_COLOR_CONV：用于记录颜色转换程序。不同的液晶屏对图像颜色的处理可能会不同，如同样是16位的颜色数据，有RGB565、RGB655等不同的格式，所以我们要根据液晶屏选择恰当的颜色转换程序，当emWin上层库函数解析颜色数据时，会调用该函数按固定格式转换。ILI9341的
颜色格式为RGB565，所以选择GUICC_M565，见图 5‑4。

|transp005|

图 5‑4 颜色转换程序

3) Flags：这个标志估计是emWin官方设计预留的，官方给出的说明是这个变量应赋值为0，没有其它解释。

4) LayerIndex：用于指定液晶驱动所对应的显示层。emWin支持以多层的形式来驱动液晶屏，多层操作可以用于驱动多屏幕或者做一些多层融合的高级操作，但多层操作对内存的消耗非常大。在我们的应用中，只用到了单层的，所以这个参数我们配置为第0层。

在LCD_X_Config 函数中，调用了GUI_DEVICE_CreateAndLink函数按上面的描述配置了指向液晶驱动的pDevice指针，GUI_DEVICE_API属性为GUIDRV_FLEXCOLOR，LCD_API_COLOR_CONV属性为GUICC_M565，Flags值为0，La
yerIndex为0，也就是代码清单 5‑4第1部分内容。

CONFIG_FLEXCOLOR类型
''''''''''''''''''

此类型的结构体用来记录关于液晶屏的基本配置选项，其原型如代码清单 5‑6所示。

代码清单 5‑6 CONFIG_FLEXCOLOR原型

1 typedef struct {

2 /\* Driver specific configuration items \*/

3 int FirstSEG;

4 int FirstCOM;

5 int Orientation;

6 U16 RegEntryMode;

7 int NumDummyReads;

8 } CONFIG_FLEXCOLOR;

1) FirstSEG和FirstCOM：分别用于配置显示控制器数据RAM使用的第一个段地址及第一个公用地址，这两个地址用于对液晶数据RAM的寻址，见图 5‑5。通常该值为0，修改该参数会使屏幕显示的数据平移。ILI9341的这两个参数都为0。

|transp006|

图 5‑5 显示数据在RAM中的组织方式

2) Orientation：这个属性用于指定液晶屏的方向。它可以使用GUI_MIRROR_X(X轴镜像显示)、GUI_MIRROR_Y(Y轴镜像显示)及GUI_SWAP_XY(交换XY轴)的组合来配置方向。关于ILI9341液晶屏的这个值的配置，我们是实验得出的，在横屏的情况下，该属性配置为GUI
_SWAP_XY \| GUI_MIRROR_Y，竖屏的情况下该属性值被配置为GUI_MIRROR_Y|GUI_MIRROR_X。

3) RegEntryMode：RegEntryMode也是用来配置液晶屏的方向的，但官方给出的说明太少，不知道如何使用。在本实验中我们不修改这个参数值，保持默认。

4) NumDummyReads：在进行液晶屏读数据操作时，第几个读操作读出的才是有效数据。如ILI9341液晶控制器在进行读数据操作时，第1个读操作是无效的，在第2次进行读操作才会读出正确的数据，所以在本实验中，我们把这个属性值设置为2。

在LCD_X_Config 函数中，由GUIDRV_FlexColor_Config函数完成这些液晶屏配置，该函数有两个输入参数，分别为GUI_DEVICE类型的指向液晶各类的指针和指向CONFIG_FLEXCOLOR类型的配置结构体，具体配置见代码清单 5‑4第2部分内容。

GUI_PORT_API类型
''''''''''''''

完成了上面的配置后，还需要为emWin提供液晶屏的基本读写函数，在emWin中，使用GUI_PORT_API类型的结构体来保存指向这些基本读写函数的指针，由用户根据液晶屏完成具体的操作，其结构体原型见代码清单 5‑7。

代码清单 5‑7 GUI_PORT_API原型

1 typedef struct {

2 /\* 8 Bit access \*/

3 void (\* pfWrite8_A0) (U8 Data);

4 void (\* pfWrite8_A1) (U8 Data);

5 void (\* pfWriteM8_A0) (U8 \* pData, int NumItems);

6 void (\* pfWriteM8_A1) (U8 \* pData, int NumItems);

7 U8 (\* pfRead8_A0) (void);

8 U8 (\* pfRead8_A1) (void);

9 void (\* pfReadM8_A0) (U8 \* pData, int NumItems);

10 void (\* pfReadM8_A1) (U8 \* pData, int NumItems);

11 /\* 16 Bit access \*/

12 void (\* pfWrite16_A0) (U16 Data);

13 void (\* pfWrite16_A1) (U16 Data);

14 void (\* pfWriteM16_A0)(U16 \* pData, int NumItems);

15 void (\* pfWriteM16_A1)(U16 \* pData, int NumItems);

16 U16 (\* pfRead16_A0) (void);

17 U16 (\* pfRead16_A1) (void);

18 void (\* pfReadM16_A0) (U16 \* pData, int NumItems);

19 void (\* pfReadM16_A1) (U16 \* pData, int NumItems);

20 /\* 32 Bit access \*/

21 void (\* pfWrite32_A0) (U32 Data);

22 void (\* pfWrite32_A1) (U32 Data);

23 void (\* pfWriteM32_A0)(U32 \* pData, int NumItems);

24 void (\* pfWriteM32_A1)(U32 \* pData, int NumItems);

25 U32 (\* pfRead32_A0) (void);

26 U32 (\* pfRead32_A1) (void);

27 void (\* pfReadM32_A0) (U32 \* pData, int NumItems);

28 void (\* pfReadM32_A1) (U32 \* pData, int NumItems);

29 /\* SPI access \*/

30 void (\* pfSetCS) (U8 NotActive);

31 /\* Common routines \*/

32 void (\* pfFlushBuffer)(void);

33 } GUI_PORT_API;

从代码清单 5‑7中可以看到，它主要定义了8位、16位及32位这三大类的液晶操作函数指针。我们使用的ILI9341是配置成16位形式的，所以在这里主要讲解16位的操作函数，其它位的函数其实是类似的，掌握原理后读者可以轻松地把emWin移植到其它液晶驱动器上。

代码清单 5‑7中用于16位液晶驱动器的操作函数有8个，但实际上并不需要实现全部8个函数，据官方说明，GUIDRV_FlexColor类型的16位液晶驱动只需要实现写一个命令字、写一个数据字、写多个数据字及读多个数据字这四个操作函数就可以了。

首先是pfWrite16_A0，它是写一个命令字的指针。在程序中我们向它赋值LcdWriteReg，一个具有向液晶屏寄存器写入命令功能的函数。函数见代码清单 5‑8。

代码清单 5‑8 LcdWriteReg函数（LCDConf_FlexColor.c文件）

1 /\* LCD驱动器的访问地址 \*/

2 #define macFSMC_ILI9341_REG \*(__IO uint16_t \*)(0x6C000000)

3

4 /\*

5 \*

6 \* LcdWriteReg

7 \*

8 \* Function description:

9 \* Sets display register

10 \*/

11 static void LcdWriteReg(U16 Data)

12 {

13 // ...
TBD by user

14 macFSMC_ILI9341_REG = Data; //modify by fire

15 }

代码非常简单，就只有一个赋值语句，操作就是向0x6C000000地址写入命令内容而已，在配置好FSMC外设的情况下，只要向这个地址写入一个数据，ILI9341液晶屏就会把它当成指令来执行，在实际操作中使用的是宏macFSMC_ILI9341_REG。

然后是pfWrite16_A1，它是写一个数据字的指针。在程序中我们向它赋值LcdWriteData，一个具有向液晶屏寄存器写入单字节数的函数。函数见代码清单 5‑9。

代码清单 5‑9 LcdWriteData函数（LCDConf_FlexColor.c文件）

1 /\* LCD驱动器的访问地址 \*/

2 #define macFSMC_ILI9341_RAM \*(__IO uint16_t \*)(0x6D000000)

3

4 /\*

5 \*

6 \* LcdWriteData

7 \*

8 \* Function description:

9 \* Writes a value to a display register

10 \*/

11 static void LcdWriteData(U16 Data)

12 {

13 // ...
TBD by user

14 macFSMC_ILI9341_RAM = Data;//modify by fire

15 }

如代码清单 5‑9所示，这个函数与前一个唯一不同的是向宏ILI9341_RAM赋值，该宏指向的地址为0x6D000000，在配置好FSMC外设的情况下，向这个地址写入一个数据，ILI9341液晶屏就把它当普通数据的形式来理解。

接着是pfWriteM16_A1，它是写多个数据字的指针。在程序中我们向它赋值LcdWriteDataMultiple，一个具有向液晶屏寄存器写入多个字节数的函数。函数见代码清单 5‑10。

代码清单 5‑10 LcdReadDataMultiple函数（LCDConf_FlexColor.c文件）

1 /\* LCD驱动器的访问地址 \*/

2 #define macFSMC_ILI9341_RAM \*(__IO uint16_t \*)(0x6D000000)

3

4 /\*

5 \*

6 \* LcdWriteDataMultiple

7 \*

8 \* Function description:

9 \* Writes multiple values to a display register.

10 \*/

11 static void LcdWriteDataMultiple(U16 \* pData, int NumItems)

12 {

13 while (NumItems--) {

14 // ...
TBD by user

15 macFSMC_ILI9341_RAM = \*pData++; //modify by fire

16 }

17 }

与上面的写单字节数据的函数有一点区别，这个多字节写函数是对液晶数据赋值语句进行NumItems次循环，很好理解。

最后是pfReadM16_A1，它是读多个数据字的指针，在程序中我们向它赋值LcdReadDataMultiple，一个具有向液晶屏寄存器写入多个字节数的函数。函数见

代码清单 5‑11 LcdReadDataMultiple函数（LCDConf_FlexColor.c文件）

1 /\* LCD驱动器的访问地址 \*/

2 #define macFSMC_ILI9341_RAM \*(__IO uint16_t \*)(0x6D000000)

3

4 /\*

5 \*

6 \* LcdReadDataMultiple

7 \*

8 \* Function description:

9 \* Reads multiple values from a display register.

10 \*/

11 static void LcdReadDataMultiple(U16 \* pData, int NumItems)

12 {

13 while (NumItems--) {

14 // ...
TBD by user

15 \*pData++ = macFSMC_ILI9341_RAM; //modify by fire

16 }

17 }

这个读数据函数是写数据函数的反操作，在做获取宏macFSMC_ILI9341_RAM这个地址中的内容时，ILI9341液晶控制器就会向STM32的FSMC外设输出数据。

所有四个函数都编写完毕后，就可以像代码清单 5‑4第3部分内容那样，对函数指针赋值。与此同时，代码清单
5‑4的最后调用了GUIDRV_FlexColor_SetFunc库函数配置液晶屏的总线、缓存及硬件程序，此函数的pfFunc及pfMode参数是根据ILI9341驱动器在《STemWin5.44参考指南》中的说明来配置的，在此不作介绍。

至此，我们就完成了LCD_X_Config函数的配置，它的代码其实很简短，野火以大篇幅的内容介绍这个函数，是为了让读者知道我们为什么这样修改代码，希望读者能够举一反三，在使用其它液晶驱动器的时候也能自己动手修改移植代码。

LCD_X_DisplayDriver函数
^^^^^^^^^^^^^^^^^^^^^

在LCDConf_FlexColor.c文件里，还有一个LCD_X_DisplayDriver函数，它是在GUI_Init库函数初始化过程中最后一个调用的，使用它可以对不同的液晶层进行控制或初始化，见代码清单 5‑12。

代码清单 5‑12 LCD_X_DisplayDriver函数（LCDConf_FlexColor.c文件）

1 /\*

2 \*

3 \* LCD_X_DisplayDriver

4 \*

5 \* Function description:

6 \* This function is called by the display driver for several purposes.

7 \* To support the according task the routine needs to be adapted to

8 \* the display controller.
Please note that the commands marked with

9 \* 'optional' are not cogently required and should only be adapted if

10 \* the display controller supports these features.

11 \*

12 \* Parameter:

13 \* LayerIndex - Index of layer to be configured

14 \* Cmd - Please refer to the details in the switch statement

15 below

16 \* pData - Pointer to a LCD_X_DATA structure

17 \*

18 \* Return Value:

19 \* < -1 - Error

20 \* -1 - Command not handled

21 \* 0 - Ok

22 \*/

23 int LCD_X_DisplayDriver(unsigned LayerIndex, unsigned Cmd, void \*

24 pData)

25 {

26 int r;

27 (void) LayerIndex;

28 (void) pData;

29

30 switch (Cmd) {

31 case LCD_X_INITCONTROLLER: {

32 /\* Called during the initialization process in order to set

33 up the display controller and put it into operation.

34 If the display controller is not initialized by any

35 external routine this needs to be adapted by the customer...

36 \*/

37 ILI9341_Init(); //modify by fire

38

39 return 0;

40 }

41 default:

42 r = -1;

43 }

44 return r;

45 }

在模版示例中我们对它的修改非常简单，只是在它的Cmd参数为LCD_X_INITCONTROLLER时，调用了我们ILI9341液晶屏的底层初始化驱动函数ILI9341_Init函数，完成了STM32的FSMC外设配置以及LCD屏基本参数初始化。

配置触摸接口
~~~~~~

触摸驱动
^^^^

emWin并没有将触摸设备接口整理为单独的.c文件，不过提供了触摸相关的库函数，用户需自行编写触摸接口。emWin针对电阻屏提供了专门的库函数，见表格 5‑2。

表格 5‑2 触摸设备API

========================== ==========================
函数名                     描述
========================== ==========================
模拟触摸屏API
GUI_TOUCH_X_ActivateX()    准备X轴的测量
GUI_TOUCH_X_ActivateY()    准备Y轴的测量
GUI_TOUCH_X_MeasureX()     返回A / D转换器的X轴结果
GUI_TOUCH_X_MeasureY()     返回A / D转换器的Y轴结果
GUI_TOUCH_Calibrate()      在运行时更改校准
GUI_TOUCH_Exec()           激活X轴和Y轴的测量
GUI_TOUCH_GetxPhys()       返回A / D转换器给出的x坐标
GUI_TOUCH_GetyPhys()       返回A / D转换器给出的Y坐标
GUI_TOUCH_SetOrientation() 设置逻辑坐标方向
========================== ==========================

emWin把触摸设备、外接鼠标和指点杆等都归为指针输入设备(Pointer input devices或PID)这一大类中。其中触摸设备API分为通用触摸屏和模拟触摸屏两种，模拟触摸屏API为电阻屏专用。

在这里我们以模版示例中使用的3.2吋电阻屏为例，讲解如何配置触摸接口。首先是触摸接口的移植，见代码清单 5‑13。

代码清单 5‑13 触摸接口移植（GUI_X_Touch_Analog.c文件）

1 #include "GUI.h"

2 #include ".\lcd\bsp_xpt2046_lcd.h"

3

4 //modify by fire

5 本文件的函数为触摸驱动，由emWin上层调用

6

7 void GUI_TOUCH_X_ActivateX(void)

8 {

9 // XPT2046_WriteCMD(0x90);

10 }

11

12

13 void GUI_TOUCH_X_ActivateY(void)

14 {

15 //XPT2046_WriteCMD(0xd0);

16 }

17

18

19 int GUI_TOUCH_X_MeasureX(void)

20 {

21 return XPT2046_ReadAdc_Fliter(macXPT2046_CHANNEL_Y);

22

23 }

24

25 int GUI_TOUCH_X_MeasureY(void)

26 {

27 return XPT2046_ReadAdc_Fliter(macXPT2046_CHANNEL_X);

28 }

29

上述代码中一共包含了4个函数，这四个函数在表格 6‑1中已有介绍，他们的具体内容需要由用户根据触摸屏驱动器来编写，在配套例程“FreeRTOS_STemWIN544_移植模版”中使用的电阻屏触摸驱动为XPT2046芯片，关于此驱动器的控制原理请参考《零死角玩转STM32》教程。

代码清单 5‑13中的前两个函数为空函数，实际上触摸数据的采集是后面两个函数完成的，为什么这么写呢？因为在实际应用中，如果把XPT2046的原始数据直接交给emWin的话，触摸屏幕光标就会随机跳动，会出现飞点。为了改善这种情况，我们使用XPT2046_ReadAdc_Fliter函数对触摸数据进行多
次采集，然后进行滤波处理，以降低触摸信号的噪声，滤波函数见代码清单 5‑14。

代码清单 5‑14 XPT2046_ReadAdc_Fliter函数（bsp_xpt2046_lcd.c文件）

1 #define SAMP_CNT 4

2 #define SAMP_CNT_DIV2 2

3 /\*

4 \\*

5 \* 函 数 名: XPT2046_ReadAdc_Fliter

6 \* 功能说明: 选择一个模拟通道，启动ADC，并返回ADC采样结果

7 \* 形 参：_ucCh = 0x90 表示Y通道； 0xd0 表示X通道

8 \* 返 回 值: 滤波后的12位ADC值

9 \\*

10 \*/

11 uint16_t XPT2046_ReadAdc_Fliter(uint8_t channel)

12 {

13 u8 i, j, min;

14 u16 temp;

15 u16 tempXY[SAMP_CNT];

16

17 static uint16_t adc_x = 0, adc_y = 0;

18

19 if (XPT2046_PENIRQ_Read() == XPT2046_PENIRQ_ActiveLevel) {

20 for (i = 0; i < SAMP_CNT; i++) {

21 XPT2046_WriteCMD(channel);

22 tempXY[i] = XPT2046_ReadCMD();

23 }

24 // 降序排列

25 for (i = 0; i < SAMP_CNT - 1; i++) {

26 min = i;

27 for (j = i + 1; j < SAMP_CNT; j++) {

28 if (tempXY[min] > tempXY[j])

29 min = j;

30 }

31 temp = tempXY[i];

32 tempXY[i] = tempXY[min];

33 tempXY[min] = temp;

34 }

35

36 // 设定阈值

37 if ((tempXY[SAMP_CNT_DIV2] - tempXY[SAMP_CNT_DIV2 - 1]) > 5) {

38 /\* 若两个中间值相差太远，则舍弃这个新数据，返回上一次的触摸数据*/

39 if (channel == macXPT2046_CHANNEL_Y)

40 return adc_x; //x通道

41 else

42 return adc_y; //y通道

43 }

44

45 // 求中间值的均值

46 if (channel == macXPT2046_CHANNEL_Y) {

47 adc_x = (tempXY[SAMP_CNT_DIV2] + tempXY[SAMP_CNT_DIV2 - 1]) / 2;

48 return adc_x;

49 } else {

50 adc_y = (tempXY[SAMP_CNT_DIV2] + tempXY[SAMP_CNT_DIV2 - 1]) / 2;

51 return adc_y;

52 }

53 } else {

54 return 0; //没有触摸，返回0

55 }

56 }

如代码清单 5‑14所示，本函数的滤波方法是，当触摸屏被按下时，采样触摸屏数据若干次，使用冒泡法排序，取中间两个数据的差值；若差值大于阈值，则丢弃。由于引入了阈值判断，我们仅对数据采样4次，所得效果已经非常令人满意。需要注意的是采样数据不宜过多，否则连续的线会变成离散的点。

触摸接口和滤波都完成后，就可以使用触摸屏了。在本章移植模板示例中，我们在单独一个任务中定时调用GUI_TOUCH_Exec函数，让emWin去采集电阻屏各通道电压值，见代码清单 5‑15。

代码清单 5‑15 Touch_Task函数（Main.c文件）

1 /*\*

2 \* @brief 触摸检测任务主体

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void Touch_Task(void\* parameter)

8 {

9 while (1) {

10 GUI_TOUCH_Exec();//触摸屏定时扫描

11 vTaskDelay(10);

12 }

13 }

如代码清单 5‑15所示，在Touch_Task任务中，每10ms调用一次GUI_TOUCH_Exec函数，为什么是10ms呢？因为每调用一次GUI_TOUCH_Exec函数只会采集一个轴的值，采集完整的一个点需要调用两次GUI_TOUCH_Exec函数。

触摸校准
^^^^

初始化校准
'''''

配置好基本的驱动接口之后，还要进行触摸校准，在LCDConf_FlexColor.c文件中的LCD_X_Config函数里添加配置触摸屏方向及触摸校准的配置，见代码清单 5‑16。

代码清单 5‑16 LCD_X_Config函数（LCDConf_FlexColor.c文件）

1 void LCD_X_Config(void)

2 {

3 GUI_DEVICE \* pDevice;

4 CONFIG_FLEXCOLOR Config = {0};

5 GUI_PORT_API PortAPI = {0};

6

7 /\* 第1部分，链接显示驱动和颜色转换程序 \*/

8 pDevice = GUI_DEVICE_CreateAndLink(GUIDRV_FLEXCOLOR, GUICC_M565, 0,

9 0);

10 /\* 设置显示区域尺寸 \*/

11 LCD_SetSizeEx (0, XSIZE_PHYS , YSIZE_PHYS);

12 LCD_SetVSizeEx(0, VXSIZE_PHYS, VYSIZE_PHYS);

13

14 /\* 第2部分，设置液晶驱动基础选项 \*/

15 Config.FirstCOM = 0; //modify by fire

16 Config.FirstSEG = 0; //modify by fire

17 Config.Orientation = GUI_MIRROR_Y|GUI_MIRROR_X;//modify by fire

18 竖屏

19 Config.NumDummyReads = 2; //modify by fire

20 读取的第二个数据才是真实数据

21

22 GUIDRV_FlexColor_Config(pDevice, &Config);

23

24 /\* 第3部分，添加液晶读写函数 \*/

25 PortAPI.pfWrite16_A0 = LcdWriteReg;

26 PortAPI.pfWrite16_A1 = LcdWriteData;

27 PortAPI.pfWriteM16_A1 = LcdWriteDataMultiple;

28 PortAPI.pfReadM16_A1 = LcdReadDataMultiple;

29 GUIDRV_FlexColor_SetFunc(pDevice, &PortAPI,

30 GUIDRV_FLEXCOLOR_F66709,

31 GUIDRV_FLEXCOLOR_M16C0B16);//modify by

32 fire

33

**34 /*第4部分，设置触摸原点 \*/**

**35 GUI_TOUCH_SetOrientation((GUI_MIRROR_X \* LCD_GetMirrorXEx(0)) \|**

**36 (GUI_MIRROR_Y \* LCD_GetMirrorYEx(0)) \|**

**37 (GUI_SWAP_XY \* LCD_GetSwapXYEx (0)));**

**38 /*设置触摸校准 \*/**

**39 GUI_TOUCH_Calibrate(GUI_COORD_X, 0, 240-1, TOUCH_AD_RIGHT ,**

**40 TOUCH_AD_LEFT );**

**41 GUI_TOUCH_Calibrate(GUI_COORD_Y, 0, 320-1,TOUCH_AD_BOTTOM ,**

**42 TOUCH_AD_TOP );**

43 }

如代码清单 5‑16粗体部分所示，在GUIDRV_FlexColor_SetFunc函数后添加了两个关于触摸屏的函数。

首先是GUI_TOUCH_SetOrientation函数，其函数原型如代码清单 5‑17所示。

代码清单 5‑17 函数原型

1 void GUI_TOUCH_SetOrientation(unsigned Orientation);

1) Orientation：触摸坐标轴方向，可使用“或”运算设置多个值。此参数可选值见表格 5‑3。

表格 5‑3 Orientation参数可选值

============ ==========
可选值       描述
============ ==========
GUI_MIRROR_X 镜像X轴
GUI_MIRROR_Y 镜像Y轴
GUI_SWAP_XY  交换X、Y轴
============ ==========

GUI_TOUCH_SetOrientation函数用于配置触摸屏方向。它配置好后可以自动与液晶屏匹配，而不需要对硬件程序进行任何的改动。代码清单 5‑16中此函数输入了3个类似参数GUI_MIRROR_X \*
LCD_GetMirrorXEx(0)这样的，其中的LCD_GetMirrorXEx函数就是在程序运行的时候获取液晶屏的状态，然后与标志位GUI_MIRROR_X相乘，得出触摸屏是否需要旋转的结果，从而实现动态匹配触摸屏与液晶屏的方向。

然后是GUI_TOUCH_Calibrate函数，其函数原型如代码清单 5‑18所示。

代码清单 5‑18 函数原型

1 int GUI_TOUCH_Calibrate(int Coord, int Log0, int Log1, int Phys0, int

2 Phys1);

1) Coord：触摸坐标轴，x轴参数是GUI_COORD_X, y轴参数是GUI_COORD_Y；

2) Log0：逻辑值0（以像素为单位）；

3) Log1：逻辑值1（以像素为单位）；

4) Phys0：Log0的A / D转换器值；

5) Phys1：Log1的A / D转换器值。

如代码清单 5‑18所示，GUI_TOUCH_Calibrate函数有5个输入参数，Coord可分别输入GUI_COORD_X或GUI_COORD_Y来配置两个方向的校准值。其中Log0和Log1分别是指对应的屏幕像素值(逻辑值)，如我们的ILI9341液晶屏分辨率为240*320，在以竖屏方式使用
时，X轴像素就是240，所以在X轴方向的配置中，Log0和Log1的值我们分别配置为0和(240-1)。Y轴的像素是320，所以我们配置其Log0及Log1值分别为0和(320-1)。Phys0及Phys1则是指对应到像素值为Log0及Log1时，触摸屏测量出的ADC原始数据，这些原始数据就是前面我
们配置触摸驱动接口时使用GUI_TOUCH_X_MeasureX和GUI_TOUCH_X_MeasureY在对应点获取到的数据。

在本章移植模板示例中我们使用了宏来表示这几个值，见代码清单 5‑19。

代码清单 5‑19 触摸ADC值对应的宏

1 #define TOUCH_AD_TOP 3670 //YPhys0

2 #define TOUCH_AD_BOTTOM 223 //YPhys1

3 #define TOUCH_AD_LEFT 208 //XPhys0

4 #define TOUCH_AD_RIGHT 3492 //XPhys1

由于不同电阻屏之间的线性度参差不齐，所以这四个宏的值需要根据情况变化。那么怎样获取这几个值呢？移植模板示例中附带了一个校准函数，让液晶屏在用户点击的位置显示“十”字，并把它的像素值坐标及物理原始数据显示出来，用户就可以通过点击屏幕，找到要配置点的像素值与触摸原始数据，记录下来修改到程序的宏即可。见代
码清单 5‑20。

代码清单 5‑20 TOUCH_First_Calibrate函数（Touch_Calibration.c文件）

1 /\*

2 \*

3 \* 使用这个来读取ADC的值

4 \*/

5 void TOUCH_First_Calibrate(void)

6 {

7 GUI_CURSOR_Show();

8 GUI_CURSOR_Select(&GUI_CursorCrossL);

9 GUI_SetBkColor(GUI_WHITE);

10 GUI_SetColor(GUI_BLACK);

11 GUI_Clear();

12 GUI_DispStringAt("Measurement of\nA/D converter values\n",0,10);

13 while (1) {

14 GUI_PID_STATE TouchState;

15 int xPhys, yPhys;

16 GUI_TOUCH_GetState(&TouchState); /\* Get the touch position in pixel \*/

17 xPhys = GUI_TOUCH_GetxPhys(); /\* Get the A/D mesurement result in x \*/

18 yPhys = GUI_TOUCH_GetyPhys(); /\* Get the A/D mesurement result in y \*/

19 /\* Display the measurement result \*/

20 GUI_SetColor(GUI_BLUE);

21 GUI_DispStringAt("Analog input:\n", 10, 30);

22 GUI_GotoY(GUI_GetDispPosY() + 2);

23 GUI_DispString("x:");

24 GUI_DispDec(xPhys, 4);

25 GUI_DispString(", y:");

26 GUI_DispDec(yPhys, 4);

27 /\* Display the according position \*/

28 GUI_SetColor(GUI_RED);

29 GUI_GotoY(GUI_GetDispPosY() + 4);

30 GUI_DispString("\nPosition:\n");

31 GUI_GotoY(GUI_GetDispPosY() + 2);

32 GUI_DispString("x:");

33 GUI_DispDec(TouchState.x,4);

34 GUI_DispString(", y:");

35 GUI_DispDec(TouchState.y,4);

36 /\* Wait a while \*/

37 GUI_Delay(100);

38 };

39 }

代码清单 5‑20的TOUCH_First_Calibrate函数的整体功能就是实现了显示触摸光标，并把光标所在的像素值、触摸原始数据值显示到屏幕上。我们在主函数中调用此函数以获取校准数据，现象如图 5‑6所示。

|transp007|

图 5‑6 TOUCH_First_Calibrate函数实验现象

图 5‑6是分别触摸点击屏幕左上角和右下角时显示的各个参数(由于截屏时不会对“十”字光标截图，为了便于观察和理解，后期将点击区域用黑色圆点表示出来，图中的点与emWin的光标实际上不同)。

整理获取到的各个触摸参数，得到一张关于逻辑值和AD采样值的表格，见表格 5‑4。

表格 5‑4 逻辑值和AD采样值表格

=================== ==== ====
点击左上角          X    Y
=================== ==== ====
Position(Log0)      0    0
Analog input(Phys0) 0208 3670
点击右下角          X    Y
Position(Log1)      239  319
Analog input(Phys1) 3492 0223
=================== ==== ====

最后，我们把整理出来的这几个AD采样值参数值修改到工程文件中如代码清单 5‑19所示的位置，就完成了触摸屏初始化校准工作。

运行时校准
'''''

上一节所讲的初始化校准实际使用起来其实挺麻烦的，换一块触摸屏可能就得重新适配，而且最关键的是需要修改程序代码，如果是做成产品，在使用过程中出现触摸不准的情况，用户也不可能去修改代码。好在emWin还提供了另一种在运行时校准触摸的方法，见代码清单 5‑21。

代码清单 5‑21 运行时校准触摸的方法

1 #define NUM_CALIB_POINTS 5 //校准点数

2

3 static int \_aRefX[NUM_CALIB_POINTS];

4 static int \_aRefY[NUM_CALIB_POINTS];

5 static int \_aSamX[NUM_CALIB_POINTS];

6 static int \_aSamY[NUM_CALIB_POINTS];

7

8 static void \_Calibrate(void)

9 {

10 GUI_PID_STATE State;

11 int i;

12 int xSize, ySize;

13

14 xSize = LCD_GetXSize();

15 ySize = LCD_GetYSize();

16

17 /\* 根据LCD尺寸计算参考点 \*/

18 \_aRefX[0] = (xSize \* 5) / 100;

19 \_aRefY[0] = (ySize \* 5) / 100;

20 \_aRefX[1] = xSize - (xSize \* 5) / 100;

21 \_aRefY[1] = \_aRefY[0];

22 \_aRefX[2] = \_aRefX[1];

23 \_aRefY[2] = ySize - (ySize \* 5) / 100;

24 \_aRefX[3] = \_aRefX[0];

25 \_aRefY[3] = \_aRefY[2];

26 \_aRefX[4] = xSize / 2;

27 \_aRefY[4] = ySize / 2;

28

29 /\* 在LCD上绘制参考点 \*/

30 GUI_TOUCH_GetState(&State);

31 State.Pressed = 0;

32 GUI_SetPenSize(3);

33 for (i = 0; i < NUM_CALIB_POINTS; i++) {

34 GUI_Clear();

35 GUI_DispStringHCenterAt("Please touch the point", LCD_GetXSize(

36 ) / 2, LCD_GetYSize() / 2 - 60);

37 GUI_DrawCircle(_aRefX[i], \_aRefY[i], 5);

38 while (State.Pressed != 1) {

39 GUI_Delay(250);

40 GUI_TOUCH_GetState(&State);

41 }

42 if (State.Pressed == 1) {

43 /\* 储存采样点 \*/

44 \_aSamX[i] = GUI_TOUCH_GetxPhys();

45 \_aSamY[i] = GUI_TOUCH_GetyPhys();

46 }

47 State.Pressed = 0;

48 GUI_Delay(250);

49 }

50

51 /\* 将测量点传递给emWin \*/

52 GUI_TOUCH_CalcCoefficients(NUM_CALIB_POINTS, \_aRefX, \_aRefY,

53 \_aSamX, \_aSamY, xSize, ySize);

54 }

如代码清单 5‑21所示，这种方法的关键就是使用了GUI_TOUCH_CalcCoefficients 函数，此函数负责计算适用于3个点以上的校准系数，并且直接在内部设置校准系数，自动完成校准。用了这个函数，就可以在程序运行的时候随时随地校准触摸屏，不再需要修改代码。

emWin校准触摸的方法，简单说就是从X轴和Y轴中取N个参考点（像素坐标）和N个样本值（AD采样结果）传递到相应的函数来完成校准，所以理论上点越多校准结果越精确。所以这也是为什么代码清单 5‑21使用5个点来校准触摸。

运行时校准的效果如图 5‑7所示，依次点击5个校准点进行校准，校准完成后会显示一个画板程序，可以用画板检测触摸是否准确，不准确就重新校准。

|transp008|

图 5‑7 运行时校准效果

.. |transp002| image:: media\transp002.png
   :width: 3.875in
   :height: 3.11659in
.. |transp003| image:: media\transp003.png
   :width: 3.78116in
   :height: 0.94792in
.. |transp004| image:: media\transp004.png
   :width: 5.76806in
   :height: 2.30722in
.. |transp005| image:: media\transp005.png
   :width: 5.76806in
   :height: 2.39267in
.. |transp006| image:: media\transp006.png
   :width: 5.16142in
   :height: 1.97638in
.. |transp007| image:: media\transp007.png
   :width: 5.38474in
   :height: 3.33292in
.. |transp008| image:: media\transp008.png
   :width: 5.76806in
   :height: 5.10713in
