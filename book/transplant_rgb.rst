.. vim: syntax=rst

移植emWin到STM32—RGB接口
==========================

本章以配套例程“FreeRTOS_STemWIN544_移植模版”为例，对emWin的执行过程和移植步骤进行解构，阅读时请打开该工程配套学习。

适用于RGB接口开发板的emWin，它的系统功能与操作系统接口的配置操作与适用于8080并口开发板的完全相同，不同的是配置内存管理、显示驱动和触摸驱动接口的配置。本章只关注不同的部分，相同部分可以参考第5章 的前3个小节，在这里就不再赘述了。

配置内存管理接口
~~~~~~~~

emWin启动时，GUI_X_Config函数是GUI_Init调用的第一个初始化函数，这个函数初始化了emWin的运行内存，代码位于GUIConf.c文件中，见代码清单 6‑1。

代码清单 6‑1 GUIConf.c文件内容

1 #include "GUI.h"

2

3 /\*

4 \*

5 \* Defines, configurable

6 \*

7 \\*

8 \*/

9 /\* 定义用于GUI的可用字节数 \*/

10 #if USE_EXTMEMHEAP

11 #define GUI_NUMBYTES (1024 \* 1024 \* 8) // x Byte (1)

12 #define GUI_EXTBUFADD (0xD2000000-GUI_NUMBYTES)//32MB (2)

13 SDRAM的最后8MB作为STemWIN动态内存

14 #else

15 #define GUI_NUMBYTES (1024 \* 110) // x KByte

16 #endif

17

18 /\*

19 \*

20 \* Static data

21 \*

22 \\*

23 \*/

24 #if USE_EXTMEMHEAP

25 static U32 HeapMem[GUI_NUMBYTES / 4] \__attribute__((at(GUI_EXTBUFADD)))

26 ; (3)

27 #else

28 static U32 extMem[GUI_NUMBYTES / 4];

29 #endif

30

31 /\*

32 \*

33 \* Public code

34 \*

35 \\*

36 \*/

37 /\*

38 \*

39 \* Get_ExtMemHeap

40 \*

41 \* Purpose:

42 \* Allocate heap from external memory

43 \*/

44 #if USE_EXTMEMHEAP

45 U32\* Get_ExtMemHeap (void)

46 {

47 return HeapMem;

48 }

49 #endif

50

51 /\*

52 \*

53 \* GUI_X_Config

54 \*

55 \* Purpose:

56 \* Called during the initialization process in order to set up the

57 \* available memory for the GUI.

58 \*/

59 void GUI_X_Config(void)

60 {

61 #ifdef USE_EXTMEMHEAP

62 GUI_ALLOC_AssignMemory(HeapMem, GUI_NUMBYTES); (4)

63 #else

64 GUI_ALLOC_AssignMemory(extMem, GUI_NUMBYTES);

65 #endif

66 }

67

上述代码中的第1部分定义了emWin所使用内存堆大小的宏GUI_NUMBYTES，第2部分定义了内存堆起始地址的宏GUI_EXTBUFADD，第3部分是利用前两个宏定义了一个静态的，大小为GUI_NUMBYTES / 4的数组HeapMem，并将数组的首地址定位到GUI_EXTBUFADD处，第4部
分利用库函数GUI_ALLOC_AssignMemory把该静态数组分配给emWin作为内存块使用。旧版例程中还用到了GUI_ALLOC_SetAvBlockSize函数配置存储块的平均大小，但在本教程使用的V5.44a版本中已经不再需要GUI_ALLOC_SetAvBlockSiz函数。

用户可通过GUI_NUMBYTES改变emWin使用的内存块大小，在本示例中为8MB。也可通过GUIConf.h中的USE_EXTMEMHEAP宏来选择emWin的内存堆是在内部RAM还是外部SDRAM。

请注意，应用程序无法使用全部的内存块空间，因为emWin的内存管理系统本身使用了其中很小的一部分，每个内存块大约需要额外的12个字节用于系统管理。并且在此分配的内存块并不会用于多帧缓冲。

配置显示驱动接口
~~~~~~~~

emWin的显示驱动接口主要包括创建显示设备和显示设备驱动回调函数，移植时主要针对GUIDRV_wf.c文件进行修改，同时也是emWin移植的重点对象。

配置显示驱动
^^^^^^

先来了解显示驱动函数LCD_X_Config的程序流程，它会在GUI_X_Config函数结束后被调用，具体见代码清单 6‑2。

代码清单 6‑2 LCD_X_Config接口（GUIDRV_wf.c文件）

1 void LCD_X_Config(void)

2 {

3 int i;

4 /\* 如果使用了多帧缓冲则初始化多帧缓冲区 \*/

5 #if (NUM_BUFFERS > 1)

6 for (i = 0; i < GUI_NUM_LAYERS; i++) {

7 GUI_MULTIBUF_ConfigEx(i, NUM_BUFFERS);

8 }

9 #endif

10 /\* 设置第一层的显示驱动程序和颜色转换 \*/

11 GUI_DEVICE_CreateAndLink(DISPLAY_DRIVER_0, COLOR_CONVERSION_0, 0,

12 0);

13 /\* 设置第1层的尺寸大小 \*/

14 LCD_SetSizeEx (0, XSIZE_0, YSIZE_0);

15 LCD_SetVSizeEx(0, XSIZE_0, YSIZE_0 \* NUM_VSCREENS);

16

17 /\* 如果使能多图层

18 则设置第二层的显示驱动程序和颜色转换及尺寸

19 \*/

20 #if (GUI_NUM_LAYERS > 1)

21 GUI_DEVICE_CreateAndLink(DISPLAY_DRIVER_1, COLOR_CONVERSION_1, 0,

22 1);

23 LCD_SetSizeEx (1, XSIZE_1, YSIZE_1);

24 LCD_SetVSizeEx(1, XSIZE_1, YSIZE_1 \* NUM_VSCREENS);

25 #endif

26

27 for (i = 0; i < GUI_NUM_LAYERS; i++) {

28 \_aPendingBuffer[i] = -1;

29 /\* 设置显存地址 \*/

30 LCD_SetVRAMAddrEx(i, (void \*)(_aAddr[i]));

31 /\* 记住色彩深度以便进一步操作 \*/

32 \_aBytesPerPixels[i] = LCD_GetBitsPerPixelEx(i) >> 3;

33 /\* 把CopyBuffer和CopyRect操作重定向为自定义函数

34 \*/

35 LCD_SetDevFunc(i, LCD_DEVFUNC_COPYBUFFER, (void(*)(void))

36 \_LCD_CopyBuffer);

37 LCD_SetDevFunc(i, LCD_DEVFUNC_COPYRECT, (void(*)(void))

38 \_LCD_CopyRect);

39 /\* 使用DMA2D重定向16bpp以下位图的绘图函数 \*/

40 if (_GetPixelformat(i) <= LTDC_Pixelformat_ARGB4444) {

41 LCD_SetDevFunc(i, LCD_DEVFUNC_FILLRECT, (void(*)(void))

42 \_LCD_FillRect);

43 LCD_SetDevFunc(i, LCD_DEVFUNC_DRAWBMP_8BPP, (void(*)(void))

44 \_LCD_DrawBitmap8bpp);

45 }

46 /\* 使用DMA2D重定向16bpp位图的绘图函数 \*/

47 if (_GetPixelformat(i) == LTDC_Pixelformat_RGB565) {

48 LCD_SetDevFunc(i, LCD_DEVFUNC_DRAWBMP_16BPP, (void(*)(void)

49 )_LCD_DrawBitmap16bpp);

50 }

51 /\* 使用DMA2D重定向32bpp位图的绘图函数 \*/

52 if (_GetPixelformat(i) == LTDC_Pixelformat_ARGB8888) {

53 LCD_SetDevFunc(i, LCD_DEVFUNC_DRAWBMP_32BPP, (void(*)(void)

54 )_LCD_DrawBitmap32bpp);

55 }

56 /\* 重定向ARGB1555颜色转换函数 \*/

57 GUICC_M1555I_SetCustColorConv(_Color2IndexBulk_M1555I_DMA2D,

58 \_Index2ColorBulk_M1555I_DMA2D);

59 /\* 重定向RGB565颜色转换函数 \*/

60 GUICC_M565_SetCustColorConv (_Color2IndexBulk_M565_DMA2D,

61 \_Index2ColorBulk_M565_DMA2D);

62 /\* 重定向ARGB4444颜色转换函数 \*/

63 GUICC_M4444I_SetCustColorConv(_Color2IndexBulk_M4444I_DMA2D,

64 \_Index2ColorBulk_M4444I_DMA2D);

65 /\* 重定向RGB888颜色转换函数 \*/

66 GUICC_M888_SetCustColorConv (_Color2IndexBulk_M888_DMA2D,

67 \_Index2ColorBulk_M888_DMA2D);

68 /\* 重定向ARGB8888颜色转换函数 \*/

69 GUICC_M8888I_SetCustColorConv(_Color2IndexBulk_M8888I_DMA2D,

70 \_Index2ColorBulk_M8888I_DMA2D);

71 /\* 使用DMA2D重定向批量Alpha混合函数 \*/

72 GUI_SetFuncAlphaBlending(_DMA_AlphaBlendingBulk);

73 /\* 使用DMA2D重定向调色板转换为索引值的函数 \*/

74 GUI_SetFuncGetpPalConvTable(_LCD_GetpPalConvTable);

75 /\* 使用DMA2D重定向单一颜色混合函数*/

76 GUI_SetFuncMixColors(_DMA_MixColors);

77 /\* 使用DMA2D重定向批量颜色混合函数*/

78 GUI_SetFuncMixColorsBulk(_LCD_MixColorsBulk);

79 /\* 使用DMA2D重定向16bpp色深内存设备绘制函数*/

80 GUI_MEMDEV_SetDrawMemdev16bppFunc(_LCD_DrawMemdev16bpp);

81 /\* 使用DMA2D重定向Alpha绘图函数*/

82 GUI_SetFuncDrawAlpha(_LCD_DrawMemdevAlpha,

83 \_LCD_DrawBitmapAlpha);

84 }

85 }

86

上述代码的说明如下：

-  第1部分。如果使用了多帧缓冲，则必须在LCD_X_Config函数的一开始初始化多帧缓冲接口并开启多帧缓冲功能。

-  第2部分。调用GUI_DEVICE_CreateAndLink函数创建显示驱动器设备和用于访问显示设备的颜色转换函数，并将显示驱动器设备链接到指定图层的设备列表中。LCD_SetSizeEx函数和LCD_SetVSizeEx函数分别设置物理屏幕和虚拟屏幕的显示区域大小。调用LCD_SetVRAMA
  ddrEx设置显存的起始地址，_aBytesPerPixels数值保存此时显示设备的色彩深度以便其他设置。移植时需要用户根据需要实现部分宏定义和变量。示例代码中输入使用的这些宏定义和变量在bsp_lcd.h头文件和GUIDRV_wf.c文件中，具体见代码清单 6‑3。

代码清单 6‑3 相关宏定义和变量 （bsp_lcd.h和GUIDRV_wf.c文件）

1 /*\* bsp_lcd.h文件 \**/

2 /\* LCD Size (Width and Height) \*/

3 #define LCD_PIXEL_WIDTH ((uint16_t)800)

4 #define LCD_PIXEL_HEIGHT ((uint16_t)480)

5

6 #define LCD_FRAME_BUFFER ((uint32_t)0xD0000000)

7

8 /*\* GUIDRV_wf.c文件 \**/

9 //

10 // Physical display size

11 //

12 #define XSIZE_PHYS LCD_PIXEL_WIDTH

13 #define YSIZE_PHYS LCD_PIXEL_HEIGHT

14

15 //

16 // Buffers / VScreens

17 //

18 #define NUM_BUFFERS 3 // Number of multiple buffers to be used

19 #define NUM_VSCREENS 1 // Number of virtual screens to be used

20

21 //

22 // BkColor shown if no layer is active

23 //

24 #define BK_COLOR GUI_DARKBLUE

25

26 //

27 // Redefine number of layers for this configuration file.
Must be

28 equal or less than in GUIConf.h!

29 //

30 #undef GUI_NUM_LAYERS

31 #define GUI_NUM_LAYERS 1

32

33 /\*

34 \*

35 \* Color mode definitions

36 \*/

37 #define \_CM_ARGB8888 1

38 #define \_CM_RGB888 2

39 #define \_CM_RGB565 3

40 #define \_CM_ARGB1555 4

41 #define \_CM_ARGB4444 5

42 #define \_CM_L8 6

43 #define \_CM_AL44 7

44 #define \_CM_AL88 8

45

46 /\*

47 \*

48 \* Layer 0

49 \*/

50 //

51 // Color mode layer 0

52 //

53 #define COLOR_MODE_0 \_CM_RGB888

54 //

55 // Layer size

56 //

57 #define XSIZE_0 LCD_PIXEL_WIDTH

58 #define YSIZE_0 LCD_PIXEL_HEIGHT

59

60 /\*

61 \*

62 \* Automatic selection of driver and color conversion

63 \*/

64 #if (COLOR_MODE_0 == \_CM_ARGB8888)

65 #define COLOR_CONVERSION_0 GUICC_M8888I

66 #define DISPLAY_DRIVER_0 GUIDRV_LIN_32

67 #elif (COLOR_MODE_0 == \_CM_RGB888)

68 #define COLOR_CONVERSION_0 GUICC_M888

69 #define DISPLAY_DRIVER_0 GUIDRV_LIN_24

70 #elif (COLOR_MODE_0 == \_CM_RGB565)

71 #define COLOR_CONVERSION_0 GUICC_M565

72 #define DISPLAY_DRIVER_0 GUIDRV_LIN_16

73 #elif (COLOR_MODE_0 == \_CM_ARGB1555)

74 #define COLOR_CONVERSION_0 GUICC_M1555I

75 #define DISPLAY_DRIVER_0 GUIDRV_LIN_16

76 #elif (COLOR_MODE_0 == \_CM_ARGB4444)

77 #define COLOR_CONVERSION_0 GUICC_M4444I

78 #define DISPLAY_DRIVER_0 GUIDRV_LIN_16

79 #elif (COLOR_MODE_0 == \_CM_L8)

80 #define COLOR_CONVERSION_0 GUICC_8666

81 #define DISPLAY_DRIVER_0 GUIDRV_LIN_8

82 #elif (COLOR_MODE_0 == \_CM_AL44)

83 #define COLOR_CONVERSION_0 GUICC_1616I

84 #define DISPLAY_DRIVER_0 GUIDRV_LIN_8

85 #elif (COLOR_MODE_0 == \_CM_AL88)

86 #define COLOR_CONVERSION_0 GUICC_88666I

87 #define DISPLAY_DRIVER_0 GUIDRV_LIN_16

88 #else

89 #error Illegal color mode 0!

90 #endif

91

92 /*\* 变量定义 \**/

93 static LTDC_Layer_TypeDef \* \_apLayer[] = { LTDC_Layer1, LTDC_Layer2 };

94 static const U32 \_aAddr[] = { LCD_FRAME_BUFFER, LCD_FRAME_BUFFER +

95 XSIZE_PHYS \* YSIZE_PHYS \* sizeof(U32) \*

96 NUM_VSCREENS \* NUM_BUFFERS };

97 static int \_aPendingBuffer[GUI_NUM_LAYERS];

98 static int \_aBufferIndex[GUI_NUM_LAYERS];

99 static int \_axSize[GUI_NUM_LAYERS];

100 static int \_aySize[GUI_NUM_LAYERS];

101 static int \_aBytesPerPixels[GUI_NUM_LAYERS];

在上述宏定义中，为了方便修改显示图层数，将 GUIConf.h中的GUI_NUM_LAYERS在这里重新定义。

GUIDRV_LIN_XX是emWin内置的液晶驱动，其中XX代表不同的色彩深度。此驱动支持所有液晶控制器，但仅管理显存内容，它不向液晶控制器发送任何命令或假定任何特殊寄存器。以这种液晶驱动为基础，可以直接通过STM32F429上的LTDC液晶控制器驱动屏幕，关于LTDC的驱动原理请参考野火的《零死
角玩转STM32》教程。GUICC_XXXX是emWin内置的各种像素格式的固定调色板，也就是颜色转换函数，其中XXXX代表不同的像素格式，当emWin上层库函数解析颜色数据时，会调用这些函数按固定格式转换。

\_aAddr数组存放了所有图层的显存地址，_aPendingBuffer和_aBufferIndex是所有图层的多帧缓冲区索引，_axSize 和_aySize 存放了所有图层的宽高，_aBytesPerPixels
存放每个图层正在使用的色彩深度索引。emWin占用的显存大小与很多参数的有关，例如LCD分辨率大小、像素格式、多帧缓冲级别等等都会对显存大小造成影响。在32位色彩深度单图层下总的显存占用大小计算方法如下：

XSIZE_PHYS \* YSIZE_PHYS \* 4 \* NUM_VSCREENS \* NUM_BUFFERS

上述公式里的4是因为32位色彩深度下单个像素数据占了4个字节，如果采用24位色彩深度则变为乘3。移植的时候需要用户根据实际情况配置。

代码清单 6‑2的第3部分。这个部分主要是针对DMA2D图形加速器接口的配置。调用LCD_SetDevFunc函数将帧缓冲区复制、复制矩形、绘制不同色彩深度位图等操作重定向到自定义接口，GUICC_XXXXX_SetCustColorConv函数为相应的固定调色板模式设置自定义颜色转换接口，GUI_
SetFuncAlphaBlending设置自定义批量Alpha混合接口，GUI_SetFuncAlphaBlending函数将位图调色板转换为索引值的接口设置为自定义接口，GUI_SetFuncMixColors和GUI_SetFuncMixColorsBulk分别设置自定义的单像素颜色混合接口和
批量颜色混合接口。移植时需要用户根据实际需要配置这些自定义接口。

配置驱动程序回调函数
^^^^^^^^^^

在讲解驱动程序回调函数的配置之前，先来讲解一个比较重要的用户接口，那就是液晶控制器初始化接口_LCD_InitController，见代码清单 6‑4。

代码清单 6‑4 \_LCD_InitController接口(GUIDRV_wf.c文件)

1 static void \_LCD_InitController(int LayerIndex)

2 {

3 LTDC_Layer_InitTypeDef LTDC_Layer_InitStruct = {0};

4 static int xSize, ySize, BytesPerLine, BitsPerPixel, i;

5 static U32 Pixelformat, Color;

6 static int Done;

7

8 if (LayerIndex >= GUI_COUNTOF(_apLayer)) {

9 return;

10 }

11 if (Done == 0) {

12 Done = 1;

13

14 //

15 // 使能LTDC行中断

16 //

17 LTDC_ITConfig(LTDC_IER_LIE, ENABLE);

18 NVIC_SetPriority(LTDC_IRQn, 0);

19 NVIC_EnableIRQ(LTDC_IRQn);

20 //

21 // Enable DMA2D transfer complete interrupt

22 //

23 DMA2D_ITConfig(DMA2D_CR_TCIE, ENABLE);

24 NVIC_SetPriority(DMA2D_IRQn, 0);

25 NVIC_EnableIRQ(DMA2D_IRQn);

26 //

27 // Clear transfer complete interrupt flag

28 //

29 DMA2D->IFCR = (U32)DMA2D_IFSR_CTCIF;

30 }

31 //

32 // 图层设置

33 //

34 xSize = LCD_GetXSizeEx(LayerIndex);

35 ySize = LCD_GetYSizeEx(LayerIndex);

36

37 /\* 层窗口配置 \*/

38 /\* 配置本层的窗口边界，注意这些参数是包含HBP HSW VBP VSW的 \*/

39 //一行的第一个起始像素，该成员值应用为 (LTDC_InitStruct.LTDC_AccumulatedHBP+1)的值

40 LTDC_Layer_InitStruct.LTDC_HorizontalStart = HBP + HSW;

41 //一行的最后一个像素，该成员值应用为 (LTDC_InitStruct.LTDC_AccumulatedActiveW)的值

42 LTDC_Layer_InitStruct.LTDC_HorizontalStop = HSW+HBP+xSize-1;

43 //一列的第一个起始像素，该成员值应用为 (LTDC_InitStruct.LTDC_AccumulatedVBP+1)的值

44 LTDC_Layer_InitStruct.LTDC_VerticalStart = VBP + VSW;

45 //一列的最后一个像素，该成员值应用为 (LTDC_InitStruct.LTDC_AccumulatedActiveH)的值

46 LTDC_Layer_InitStruct.LTDC_VerticalStop = VSW+VBP+ySize-1;

47 //

48 // 像素格式设置

49 //

50 Pixelformat = \_GetPixelformat(LayerIndex);

51 LTDC_Layer_InitStruct.LTDC_PixelFormat = Pixelformat;

52 //

53 // Alpha constant (255 totally opaque)

54 //

55 LTDC_Layer_InitStruct.LTDC_ConstantAlpha = 255;

56 //

57 // Default Color configuration (configure A, R, G, B component values)

58 //

59 LTDC_Layer_InitStruct.LTDC_DefaultColorBlue = 0;

60 LTDC_Layer_InitStruct.LTDC_DefaultColorGreen = 0;

61 LTDC_Layer_InitStruct.LTDC_DefaultColorRed = 0;

62 LTDC_Layer_InitStruct.LTDC_DefaultColorAlpha = 0;

63 //

64 // Configure blending factors

65 //

66 BytesPerLine = \_GetBytesPerLine(LayerIndex, xSize);

67 LTDC_Layer_InitStruct.LTDC_BlendingFactor_1 = LTDC_BlendingFactor1_PAxCA;;

68 LTDC_Layer_InitStruct.LTDC_BlendingFactor_2 = LTDC_BlendingFactor2_PAxCA;

69 LTDC_Layer_InitStruct.LTDC_CFBLineLength = BytesPerLine + 3;

70 LTDC_Layer_InitStruct.LTDC_CFBPitch = BytesPerLine;

71 LTDC_Layer_InitStruct.LTDC_CFBLineNumber = ySize;

72 //

73 // Input Address configuration

74 //

75 LTDC_Layer_InitStruct.LTDC_CFBStartAdress = \_aAddr[LayerIndex];

76 LTDC_LayerInit(_apLayer[LayerIndex], &LTDC_Layer_InitStruct);

77 //

78 // Enable LUT on demand

79 //

80 BitsPerPixel = LCD_GetBitsPerPixelEx(LayerIndex);

81 if (BitsPerPixel <= 8) {

82 //

83 // Enable usage of LUT for all modes with <= 8bpp

84 //

85 \_LTDC_LayerEnableLUT(_apLayer[LayerIndex], ENABLE);

86 } else {

87 //

88 // Optional CLUT initialization for AL88 mode (16bpp)

89 //

90 if (_apColorConvAPI[LayerIndex] == GUICC_88666I) {

91 \_LTDC_LayerEnableLUT(_apLayer[LayerIndex], ENABLE);

92 for (i = 0; i < 256; i++) {

93 Color = LCD_API_ColorConv_8666.pfIndex2Color(i);

94 \_LTDC_SetLUTEntry(LayerIndex, Color, i);

95 }

96 }

97 }

98 //

99 // Enable layer

100 //

101 LTDC_LayerCmd(_apLayer[LayerIndex], ENABLE);

102 //

103 // Reload configuration

104 //

105 LTDC_ReloadConfig(LTDC_SRCR_IMR);

106 }

107

液晶控制器接口_LCD_InitController首先进行了LTDC的硬件初始化，然后对LCD的图层进行初始化配置相关属性，最后为小于8位色深和AL88像素格式的图形开启颜色查找表。不过在本示例中我们没有在_LCD_InitController接口中做LTDC的硬件初始，而是将其放在了GUI_In
it之前。移植时需要用户根据实际情况配置接口。

接下来是LCD_X_DisplayDriver函数，此函数是在GUI_Init库函数初始化过程中最后一个调用的。通常是由内部液晶驱动程序调用，用于管理液晶控制器的运行和设置。在GUI_Init函数中调用主要是为了初始化液晶控制器，见代码清单 6‑5。

代码清单 6‑5 LCD_X_DisplayDriver函数（GUIDRV_wf.c文件）

1 int LCD_X_DisplayDriver(unsigned LayerIndex, unsigned Cmd, void \*

2 pData)

3 {

4 int r = 0;

5

6 switch (Cmd) {

7 case LCD_X_INITCONTROLLER: {

8 /\* Called during the initialization process in order to set up

9 the display controller and put it into operation.
\*/

10 \_LCD_InitController(LayerIndex);

11 break;

12 }

13 case LCD_X_SETORG: {

14 /\* Required for setting the display origin which is passed in

15 the 'xPos' and 'yPos' element of p \*/

16 LCD_X_SETORG_INFO \* p;

17

18 p = (LCD_X_SETORG_INFO \*)pData;

19 \_apLayer[LayerIndex]->CFBAR = \_aAddr[LayerIndex] + p->yPos \*

20 \_axSize[LayerIndex] \*

21 \_aBytesPerPixels[LayerIndex];

22 LTDC_ReloadConfig(LTDC_SRCR_VBR); // Reload on next blanking

23 period

24 break;

25 }

26 case LCD_X_SHOWBUFFER: {

27 /\* Required if multiple buffers are used.
The 'Index' element

28 of p contains the buffer index.
\*/

29 LCD_X_SHOWBUFFER_INFO \* p;

30

31 p = (LCD_X_SHOWBUFFER_INFO \*)pData;

32 \_aPendingBuffer[LayerIndex] = p->Index;

33 break;

34 }

35 case LCD_X_SETLUTENTRY: {

36 /\* Required for setting a lookup table entry which is passed

37 in the 'Pos' and 'Color' element of p \*/

38 LCD_X_SETLUTENTRY_INFO \* p;

39

40 p = (LCD_X_SETLUTENTRY_INFO \*)pData;

41 \_LTDC_SetLUTEntry(LayerIndex, p->Color, p->Pos);

42 break;

43 }

44 case LCD_X_ON: {

45 /\* Required if the display controller should support switching

46 on and off \*/

47 \_LCD_DisplayOn();

48 break;

49 }

50 case LCD_X_OFF: {

51 /\* Required if the display controller should support switching

52 on and off \*/

53 \_LCD_DisplayOff();

54 break;

55 }

56 case LCD_X_SETVIS: {

57 /\* Required for setting the layer visibility which is passed

58 in the 'OnOff' element of pData \*/

59 LCD_X_SETVIS_INFO \* p;

60

61 p = (LCD_X_SETVIS_INFO \*)pData;

62 LTDC_LayerCmd(_apLayer[LayerIndex], p->OnOff ? ENABLE :

63 DISABLE);

64 break;

65 }

66 case LCD_X_SETPOS: {

67 /\* Required for setting the layer position which is passed in

68 the 'xPos' and 'yPos' element of pData \*/

69 LCD_X_SETPOS_INFO \* p;

70

71 p = (LCD_X_SETPOS_INFO \*)pData;

72 \_LTDC_SetLayerPos(LayerIndex, p->xPos, p->yPos);

73 break;

74 }

75 case LCD_X_SETSIZE: {

76 /\* Required for setting the layer position which is passed in

77 the 'xPos' and 'yPos' element of pData \*/

78 LCD_X_SETSIZE_INFO \* p;

79 int xPos, yPos;

80

81 GUI_GetLayerPosEx(LayerIndex, &xPos, &yPos);

82 p = (LCD_X_SETSIZE_INFO \*)pData;

83 \_axSize[LayerIndex] = p->xSize;

84 \_aySize[LayerIndex] = p->ySize;

85 \_LTDC_SetLayerPos(LayerIndex, xPos, yPos);

86 break;

87 }

88 case LCD_X_SETALPHA: {

89 /\* Required for setting the alpha value which is passed in the

90 'Alpha' element of pData \*/

91 LCD_X_SETALPHA_INFO \* p;

92

93 p = (LCD_X_SETALPHA_INFO \*)pData;

94 \_LTDC_SetLayerAlpha(LayerIndex, p->Alpha);

95 break;

96 }

97 case LCD_X_SETCHROMAMODE: {

98 /\* Required for setting the chroma mode which is passed in the

99 'ChromaMode' element of pData \*/

100 LCD_X_SETCHROMAMODE_INFO \* p;

101

102 p = (LCD_X_SETCHROMAMODE_INFO \*)pData;

103 \_LTDC_LayerEnableColorKeying(_apLayer[LayerIndex], (p->

104 ChromaMode != 0) ? ENABLE :

105 DISABLE);

106 break;

107 }

108 case LCD_X_SETCHROMA: {

109 /\* Required for setting the chroma value which is passed in

110 the 'ChromaMin' and 'ChromaMax' element of pData \*/

111 LCD_X_SETCHROMA_INFO \* p;

112 U32 Color;

113

114 p = (LCD_X_SETCHROMA_INFO \*)pData;

115 Color = ((p->ChromaMin & 0xFF0000) >> 16) \| (p->ChromaMin &

116 0x00FF00) \| ((p->ChromaMin & 0x0000FF) << 16);

117 \_apLayer[LayerIndex]->CKCR = Color;

118 LTDC_ReloadConfig(LTDC_SRCR_VBR); // Reload on next blanking

119 period

120 break;

121 }

122 default:

123 r = -1;

124 }

125 return r;

126

127

该回调函数将命令和命令参数作为入口参数。传入的命令告诉回调函数应执行哪些动作，如果命令带有参数，则这些参数通过数据指针 pData 传递。

上述代码中各个命令的具体含义如下：

-  LCD_X_INITCONTROLLER：初始化液晶控制器，此命令不传递任何参数。在本示例中实际上做的是LCD图层初始化，LCD的硬件初始化被放到了GUI_Init之前的BSP初始化函数中。

-  LCD_X_SETORG：设置显示的原点，或修改帧缓冲区起始地址，带有一个指向LCD_X_SETORG_INFO类型结构体的参数，原型见代码清单 6‑6。

代码清单 6‑6 LCD_X_SETORG_INFO原型

1 typedef struct {

2 int xPos, yPos;

3 } LCD_X_SETORG_INFO;

-  LCD_X_SHOWBUFFER：此命令通常与多帧缓冲一起使用，带有一个指向LCD_X_SHOWBUFFER_INFO类型结构体的参数，原型见代码清单
  6‑7。它告诉回调函数，需要将具有指定索引的多帧缓冲区变为可见。设置多帧缓冲区可见的方式有两种，一种是通过立即设置正确的多帧缓冲区起始地址，或在LTDC的帧结束中断回调函数中执行相应操作。

代码清单 6‑7 LCD_X_SHOWBUFFER_INFO原型

1 typedef struct {

2 int Index;

3 } LCD_X_SHOWBUFFER_INFO;

-  LCD_X_SETLUTENTRY：设置颜色查找表入口地址和此地址对应的RGB值，此命令带有一个指向LCD_X_SETLUTENTRY_INFO类型结构体的参数，原型见代码清单 6‑8。

代码清单 6‑8 LCD_X_SETLUTENTRY_INFO原型

1 typedef struct {

2 LCD_COLOR Color;

3 U8 Pos;

4 } LCD_X_SETLUTENTRY_INFO;

-  LCD_X_ON和LCD_X_OFF：控制屏幕的开启和关闭，此命令不传递任何参数。

-  LCD_X_SETVIS：用于在运行时设置图层的可见性，带有一个指向LCD_X_SETVIS_INFO类型结构体的参数，原型见代码清单 6‑9。

代码清单 6‑9 LCD_X_SETVIS_INFO原型

1 typedef struct {

2 int OnOff;

3 } LCD_X_SETVIS_INFO;

-  LCD_X_SETPOS：用于设置图层位置，此命令带有一个指向LCD_X_SETPOS_INFO类型结构体的参数，原型见代码清单
  6‑10。通常图层不能超过屏幕的物理范围，不过可以在不修改多帧缓冲区内容的情况下，实现图层位于显示屏外部的效果。这种效果可用于将图片移动到屏幕的可见区域或将其移出屏幕，而无需消耗额外的CPU负载进行重绘。

代码清单 6‑10 LCD_X_SETPOS_INFO原型

1 typedef struct {

2 int xPos, yPos;

3 int xLen, yLen;

4 int BytesPerPixel;

5 U32 Off;

6 } LCD_X_SETPOS_INFO;

-  LCD_X_SETSIZE：用于在运行时设置图层的大小，带有一个指向LCD_X_SETSIZE_INFO类型的参数，见代码清单 6‑11。

代码清单 6‑11 LCD_X_SETSIZE_INFO原型

1 typedef struct {

2 int xSize, ySize;

3 } LCD_X_SETSIZE_INFO;

-  LCD_X_SETALPHA：设置所需的图层alpha 值，带有一个指向LCD_X_SETALPHA_INFO类型的参数，见代码清单 6‑12。

代码清单 6‑12 LCD_X_SETALPHA_INFO原型

1 typedef struct {

2 int Alpha;

3 } LCD_X_SETALPHA_INFO;

-  LCD_X_SETCHROMAMODE：开启或关闭色度混合模式，带有一个指向LCD_X_SETCHROMAMODE_INFO类型的参数，见代码清单 6‑13。

代码清单 6‑13 LCD_X_SETCHROMAMODE_INFO原型

1 typedef struct {

2 int ChromaMode;

3 } LCD_X_SETCHROMAMODE_INFO;

-  LCD_X_SETCHROMA：设置用于色度模式的颜色，带有一个指向LCD_X_SETCHROMA_INFO类型的参数，见代码清单 6‑14。

代码清单 6‑14 LCD_X_SETCHROMA_INFO原型

1 typedef struct {

2 LCD_COLOR ChromaMin;

3 LCD_COLOR ChromaMax;

4 } LCD_X_SETCHROMA_INFO;

以上这些驱动程序回调函数命令的执行接口，移植时用户需要根据自己的LCD控制器进行配置。

配置触摸接口
~~~~~~

emWin并没有将触摸设备接口整理为单独的.c文件，不过提供了触摸相关的库函数，用户需自行编写触摸接口。emWin提供了两套不同的库函数，见表格 6‑1。

表格 6‑1 触摸设备API

======================== ============================================
函数名                   描述
======================== ============================================
通用触摸屏API
GUI_TOUCH_GetState()     返回触摸屏的当前状态
GUI_TOUCH_StoreState()   使用X坐标和Y坐标作为参数存储触摸屏的当前状态
GUI_TOUCH_StoreStateEx() 存储触摸屏的当前状态
======================== ============================================

emWin把触摸设备、外接鼠标和指点杆等都归为指针输入设备(Pointer input
devices或PID)这一大类中。其中触摸设备API分为通用触摸屏和模拟触摸屏两种，模拟触摸屏API为电阻屏专用。在这里我们以模版示例中使用的5吋电容屏为例，讲解如何配置触摸接口。触摸接口的配置和移植都非常简单，以本示例中的GT9xx_GetOnePiont为例，见代码清单 6‑15。

代码清单 6‑15 触摸接口移植（gt9xx.c文件）

1 void GT9xx_GetOnePiont(void)

2 {

3 GUI_PID_STATE State;

4 GTP_DEBUG_FUNC();

5

6 \__disable_irq();

7

8 Goodix_TS_Work_Func();

9

10 \__enable_irq();

11

12 if ((pre_x==-1) \|\| (pre_y==-1)) {

13 State.x = -1;

14 State.y = -1;

15 State.Pressed = 0;

16 State.Layer = 0;

17 GUI_TOUCH_StoreStateEx(&State);

18 return;

19 }

20 State.Pressed = 1;

21 State.x = pre_x;

22 State.y =pre_y;

23 State.Layer = 0;

24 GUI_TOUCH_StoreStateEx(&State);

25 }

GT9xx_GetOnePiont位于触摸屏驱动文件gt9xx.c中。它的功能是读取触摸坐标，并返回触摸屏的状态，它通过调用gt9xx.c文件中的Goodix_TS_Work_Func函数读取触摸坐标，该函数根据原驱动中的同名函数修改而来，只读取一个触摸点，关于触摸屏的控制原理请参考《零死角玩转ST
M32》教程。GUI_PID_STATE类型的变量State，用来存放触摸设备的触摸坐标、状态和激活层数等信息，原型见代码清单 6‑16。

代码清单 6‑16 GUI_PID_STATE原型

1 typedef struct {

2 int x,y;

3 U8 Pressed;

4 U8 Layer;

5 } GUI_PID_STATE;

配置触摸驱动时需要注意，原gt9xx.c文件中使用了中断读取触摸坐标，而本示例通过在FreeRTOS中创建一个触摸任务每20ms调用一次GT9xx_GetOnePiont函数读取触摸信息，所以触摸驱动移植时需要关闭触摸中断。
