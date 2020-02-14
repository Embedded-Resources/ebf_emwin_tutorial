.. vim: syntax=rst

颜色
=====

emWin支持不同程度的色彩显示，比如彩色显示、多阶灰度显示和黑/白显示等。用户现有的emWin应用程序只需修改位于GUIDRV_wf.c中的显示配置，就可以在不同类型的显示器上正常使用。

逻辑颜色和物理颜色
~~~~~~~~~

在emWin中颜色分为逻辑颜色和物理颜色。其中逻辑颜色是应用程序处理的颜色，包含三个8位的颜色分量和一个8位的alpha通道。在emWin V5.30之前的很长一段时间内，逻辑颜色都只有ABGR这一种颜色格式，如表格 12‑1所示。

表格 12‑1 ABGR逻辑颜色格式

============== =========== =========== ===========
Alpha          蓝色        绿色        红色
============== =========== =========== ===========
0x00：不透明

0xFF：完全透明                          DB31 – DB24    DB23 – DB16 DB15 – DB08 DB07 – DB00 ============== =========== =========== ===========

随着越来越多的硬件平台开始使用和ABGR稍微不同的颜色格式，emWin从V5.30开始增加了对ARGB逻辑颜色格式的支持，以便在某些情况下能够显着提高性能。ARGB逻辑颜色格式构成如表格 12‑2所示。

表格 12‑2 ARGB逻辑颜色格式

============== =========== =========== ===========
Alpha          红色        绿色        蓝色
============== =========== =========== ===========
0x00：完全透明

0xFF：不透明                            DB31 – DB24    DB23 – DB16 DB15 – DB08 DB07 – DB00 ============== =========== =========== ===========

从上表可以看出，ARGB逻辑颜色格式除了将蓝色和红色调换了位置以外，Alpha通道值的定义也完全相反。

由于STM32F429的DMA2D采用的就是ARGB颜色格式，所以我们所有的例程全部使用ARGB格式。因为emWin的逻辑颜色格式跟DMA2D的格式相同的话可以提升性能和运行效率。如果还是采用原来的ABGR格式，显示的时候每个像素都要进行格式转换，这样对图形性能有较大影响，特别是显示24位和32位色
彩深度的图形时影响会很大。如何开启ARGB颜色格式在emWin移植章节已有介绍，在此不再赘述。

物理颜色是显示器实际显示的颜色，在运行时，逻辑颜色映射到物理颜色。

预定义的颜色
~~~~~~

emWin预定义了一些标准颜色，这些颜色使用宏定义来表示，如图 12‑1所示。

|colour002|

图 12‑1 emWin预定义颜色

这里需要注意一点，图 12‑1是官方手册给出的标准颜色宏定义，使用的是ABGR颜色格式。程序中则给出了ARGB和ABGR两种格式下的标准颜色宏定义，见代码清单 12‑1。

代码清单 12‑1 标准颜色定义

1 /\*

2 \*

3 \* Standard colors

4 \*/

**5 #if (GUI_USE_ARGB) //ARGB格式的标准颜色定义**

6 #define GUI_BLUE 0xFF0000FF

7 #define GUI_GREEN 0xFF00FF00

8 #define GUI_RED 0xFFFF0000

9 #define GUI_CYAN 0xFF00FFFF

10 #define GUI_MAGENTA 0xFFFF00FF

11 #define GUI_YELLOW 0xFFFFFF00

12 #define GUI_LIGHTBLUE 0xFF8080FF

13 #define GUI_LIGHTGREEN 0xFF80FF80

14 #define GUI_LIGHTRED 0xFFFF8080

15 #define GUI_LIGHTCYAN 0xFF80FFFF

16 #define GUI_LIGHTMAGENTA 0xFFFF80FF

17 #define GUI_LIGHTYELLOW 0xFFFFFF80

18 #define GUI_DARKBLUE 0xFF000080

19 #define GUI_DARKGREEN 0xFF008000

20 #define GUI_DARKRED 0xFF800000

21 #define GUI_DARKCYAN 0xFF008080

22 #define GUI_DARKMAGENTA 0xFF800080

23 #define GUI_DARKYELLOW 0xFF808000

24 #define GUI_WHITE 0xFFFFFFFF

25 #define GUI_LIGHTGRAY 0xFFD3D3D3

26 #define GUI_GRAY 0xFF808080

27 #define GUI_DARKGRAY 0xFF404040

28 #define GUI_BLACK 0xFF000000

29 #define GUI_BROWN 0xFFA52A2A

30 #define GUI_ORANGE 0xFFFFA500

31 #define GUI_TRANSPARENT 0x00000000

32

33 #define GUI_GRAY_3F 0xFF3F3F3F

34 #define GUI_GRAY_50 0xFF505050

35 #define GUI_GRAY_55 0xFF555555

36 #define GUI_GRAY_60 0xFF606060

37 #define GUI_GRAY_7C 0xFF7C7C7C

38 #define GUI_GRAY_9A 0xFF9A9A9A

39 #define GUI_GRAY_AA 0xFFAAAAAA

40 #define GUI_GRAY_C0 0xFFC0C0C0

41 #define GUI_GRAY_C8 0xFFC8C8C8

42 #define GUI_GRAY_D0 0xFFD0D0D0

43 #define GUI_GRAY_E7 0xFFE7E7E7

44 #define GUI_BLUE_98 0xFF000098

**45 #else //ABGR格式的标准颜色定义**

46 #define GUI_BLUE 0x00FF0000

47 #define GUI_GREEN 0x0000FF00

48 #define GUI_RED 0x000000FF

49 #define GUI_CYAN 0x00FFFF00

50 #define GUI_MAGENTA 0x00FF00FF

51 #define GUI_YELLOW 0x0000FFFF

52 #define GUI_LIGHTBLUE 0x00FF8080

53 #define GUI_LIGHTGREEN 0x0080FF80

54 #define GUI_LIGHTRED 0x008080FF

55 #define GUI_LIGHTCYAN 0x00FFFF80

56 #define GUI_LIGHTMAGENTA 0x00FF80FF

57 #define GUI_LIGHTYELLOW 0x0080FFFF

58 #define GUI_DARKBLUE 0x00800000

59 #define GUI_DARKGREEN 0x00008000

60 #define GUI_DARKRED 0x00000080

61 #define GUI_DARKCYAN 0x00808000

62 #define GUI_DARKMAGENTA 0x00800080

63 #define GUI_DARKYELLOW 0x00008080

64 #define GUI_WHITE 0x00FFFFFF

65 #define GUI_LIGHTGRAY 0x00D3D3D3

66 #define GUI_GRAY 0x00808080

67 #define GUI_DARKGRAY 0x00404040

68 #define GUI_BLACK 0x00000000

69 #define GUI_BROWN 0x002A2AA5

70 #define GUI_ORANGE 0x0000A5FF

71 #define GUI_TRANSPARENT 0xFF000000

72

73 #define GUI_GRAY_3F 0x003F3F3F

74 #define GUI_GRAY_50 0x00505050

75 #define GUI_GRAY_55 0x00555555

76 #define GUI_GRAY_60 0x00606060

77 #define GUI_GRAY_7C 0x007C7C7C

78 #define GUI_GRAY_9A 0x009A9A9A

79 #define GUI_GRAY_AA 0x00AAAAAA

80 #define GUI_GRAY_C0 0x00C0C0C0

81 #define GUI_GRAY_C8 0x00C8C8C8

82 #define GUI_GRAY_D0 0x00D0D0D0

83 #define GUI_GRAY_E7 0x00E7E7E7

84 #define GUI_BLUE_98 0x00980000

85 #endif

从代码清单 12‑1可以看出，似乎是有一部分颜色完全透明，另一部分完全不透明。实际上除了GUI_TRANSPARENT以外所有的这些预定义颜色都是默认设置为完全不透明的，但由于两种逻辑颜色格式对Alpha通道数值大小的定义相反，所以看起来会有些差别。

固定调色板
~~~~~

emWin内置了非常多的固定调色板模式，我们在这里只列举下面几种，见表格 12‑3。完整的调色板表格请参考《STemWin5.44参考手册》。

表格 12‑3 emWin部分固定调色板

.. list-table::
   :widths: 33 33 33
   :header-rows: 0


   * - 标识符       | 可
     - 颜色              | 掩码
     - |

   * - GUICC_1
     - 黑和白                | 0
     - 01 -> 00000001         |

   * - GUICC_1616I
     - 16+4位Alpha混合       | 0
     - FF -> 11111111         |

   * - GUICC_8666
     - 232
     - 0xFF-> 232种颜色可用     |

   * - GUICC_88666I
     - 232+8位Alpha混合      | 0
     - FFFF->                 | AAAAAAAACCCCCCCC

   * - GUICC_M4444I
     - 4096+4位Alpha混合     | 0
     - FFFF ->                | AAAARRRRGGGGBBBB

   * - GUICC_M1555I
     - 32768+1位透明度       | 0x
     - FFF ->                | TRRRRRGGGGGBBBBB

   * - GUICC_565
     - 65535
     - 0xFFFF -> BBBBBGGGGGGRRRRR

   * - GUICC_M565
     - 65535
     - 0xFFFF -> RRRRRGGGGGGBBBBB

   * - GUICC_M888
     - 16777216
     - 0x00FFFFFF-> RRRRRRRRGGGGGGGGBBBBBBBB

   * - GUICC_M8888I
     - 16777216+8位Alpha混合 | 0
     - FFFFFFFF->             | AAAAAAAA RRRRRRRRGGGGGGGGBBBBBBBB


可能有读者会发现，上面的表格中调色板的标识符有些带有字母M，而另外一些则不带字母M。这也是跟之前的逻辑颜色格式有关，含字母M的表示ARGB颜色格式下使用的调色板，不带M的表示在ABGR格式下使用的调色板。

在这里我们简单介绍下两个计算机图形学中的概念，一个叫色彩深度，另一个叫像素格式。

1. 色彩深度(color depth)，也称为位深度(bit depth)，表示图像中存储一个像素的色彩信息所占用的位数，单位是位/像素(**bits per
   pixel**)或bpp。通常说的什么8位、24位图像，这个多少位指的就是色彩深度。色彩深度越大，则单个像素包含的色彩信息越多，图像整体的颜色就越丰富。常见的色彩深度有1bpp、4bpp、8bpp、16bpp、24bpp和32bpp。

2. 像素格式(pixel format)，它表示为一个像素的颜色信息以什么样的方式和顺序进行存储。例如RGB565表示用16位的色彩深度存储单个像素的颜色信息，从高位到低位依次存放红绿蓝三色，其中红色和蓝色占5位，绿色占6位。同一种色彩深度可以对应不同的像素格式。还是刚才的RGB565像素格式，这次我交
   换红色和蓝色的存放顺序，就变成了另一种像素格式BGR565，但色彩深度还是16位。

颜色API
~~~~~

表格 12‑4列出了颜色显示相关的API函数。

表格 12‑4 颜色API

======================= ==========================
函数名                  描述
======================= ==========================
基本函数
GUI_GetBkColor()        返回当前背景颜色
GUI_GetBkColorIndex()   返回当前背景颜色的索引
GUI_GetColor()          返回当前前景色
GUI_GetColorIndex()     返回当前前景色的索引
GUI_GetDefaultColor()   返回默认的前景色
GUI_GetDefaultBkColor() 返回默认背景颜色
GUI_SetBkColor()        设置当前背景颜色
GUI_SetBkColorIndex()   设置当前背景颜色的索引
GUI_SetColor()          设置当前前景色
GUI_SetColorIndex()     设置当前前景色的索引
GUI_SetDefaultColor()   设置默认前景色
GUI_SetDefaultBkColor() 设置默认背景颜色
颜色转换函数
GUI_CalcColorDist()     返回2种颜色之间的差异
GUI_CalcVisColorError() 返回与下一个可用颜色的差异
GUI_Color2Index()       将颜色转换为颜色索引
GUI_Color2VisColor()    返回最近的可用颜色
GUI_ColorIsAvailable()  检查是否有可用的颜色
GUI_Index2Color()       将颜色索引转换为颜色
======================= ==========================

这些颜色API函数里面颜色转换函数很少会用到。最常用的应该就是GUI_SetBkColor()和GUI_SetColor()了，这两个函数分别用来设置背景色和前景色。

颜色显示实验
~~~~~~

我们的颜色显示例程是从官方例程修改而来，完整的例程读者可参考位于模拟工程中的COLOR_ShowColorBar.c例程。

代码分析
^^^^

代码清单 12‑2 \_DemoShowColorBar函数（ColorBar_Task.c）

1 static const BAR_DATA \_aBarData[] = {

2 { 2, GUI_RED , "Red" },

3 { 2, GUI_GREEN , "Green" },

4 { 2, GUI_BLUE , "Blue" },

5 { 1, GUI_WHITE , "Grey" },

6 { 2, GUI_YELLOW , "Yellow" },

7 { 2, GUI_CYAN , "Cyan" },

8 { 2, GUI_MAGENTA, "Magenta" },

9 };

10

11 static const GUI_COLOR \_aColorStart[] = { GUI_BLACK, GUI_WHITE };

12

13 /*\*

14 \* @brief 色条显示函数

15 \* @note 无

16 \* @param 无

17 \* @retval 无

18 \*/

19 static void \_DemoShowColorBar(void)

20 {

21 GUI_RECT Rect;

22 int yStep;

23 int i;

24 int j;

25 int xSize;

26 int ySize;

27 int NumBars;

28 int NumColors;

29

30 xSize = LCD_GetXSize();

31 ySize = LCD_GetYSize();

32

33 /\* 可以显示的色条数 \*/

34 NumColors = GUI_COUNTOF(_aBarData);

35 for (i = NumBars = 0, NumBars = 0; i < NumColors; i++) {

36 NumBars += \_aBarData[i].NumBars;

37 }

38 yStep = (ySize - Y_START) / NumBars;

39

40 /\* 显示文本 \*/

41 Rect.x0 = 0;

42 Rect.x1 = X_START - 1;

43 Rect.y0 = Y_START;

44 GUI_SetFont(&GUI_Font16B_ASCII);

45 for (i = 0; i < NumColors; i++) {

46 Rect.y1 = Rect.y0 + yStep \* \_aBarData[i].NumBars - 1;

47 GUI_DispStringInRect(_aBarData[i].s, &Rect, GUI_TA_LEFT \|

48 GUI_TA_VCENTER);

49 Rect.y0 = Rect.y1 + 1;

50 }

51

52 /\* 绘制色条 \*/

53 Rect.x0 = X_START;

54 Rect.x1 = xSize - 1;

55 Rect.y0 = Y_START;

56 for (i = 0; i < NumColors; i++) {

57 for (j = 0; j < \_aBarData[i].NumBars; j++) {

58 Rect.y1 = Rect.y0 + yStep - 1;

59 GUI_DrawGradientH(Rect.x0, Rect.y0, Rect.x1, Rect.y1,

60 \_aColorStart[j], \_aBarData[i].Color);

61 Rect.y0 = Rect.y1 + 1;

62 }

63 }

64 }

65

首先，根据屏幕大小计算出可以显示多少色条，然后将每条色条的名称通过GUI_DispStringInRect函数显示出来，最后调用GUI_DrawGradientH函数根据指定的颜色，绘制出所有的色条。

实验现象
^^^^

在RGB888像素格式下的颜色显示实验结果如图 12‑2所示。实际在LCD屏幕上看到的色条可能稍微会有一些不连续。读者可以更改GUIDRV_wf.c中的颜色模式，看看不同像素格式下的色条显示情况。

|colour003|

图 12‑2 实验结果

.. |colour002| image:: media\colour002.png
   :width: 2.62393in
   :height: 3.28125in
.. |colour003| image:: media\colour003.png
   :width: 5.76806in
   :height: 3.46083in
