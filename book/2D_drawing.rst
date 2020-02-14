.. vim: syntax=rst

2D绘图
========

emWin 包含一个完整的2D绘图图形库，提供了非常多的API函数，包括画点、画线，绘制矩形、圆、椭圆、扇形、多边形和位图等平面图形，甚至还能生成二维码。本章节就为大家讲解2D图形库的一些基本绘图函数，图形库中的位图绘制和显示我们放到下一章节单独讲解。表格
10‑1列出了部分比较常用的2D绘图API函数。

表格 10‑1较为常用的基本2D绘图API函数表

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 函数名                     | 描
     - |

   * - 绘制相关功能               |
     - |

   * - GUI_AddRect()
     - 调整矩形大小                           |

   * - GUI_GetClientRect()
     - 返回当前可用的绘图区域                 |

   * - GUI_GetDrawMode()
     - 返回当前的绘图模式                     |

   * - GUI_GetPenSize()
     - 返回当前的画笔大小                     |

   * - GUI_GetPixelIndex()
     - 返回指定位置的颜色索引                 |

   * - GUI_SetDrawMode()
     - 设置绘图模式                           |

   * - GUI_SetPenSize()
     - 设置画笔大小                           |

   * - 基本绘图函数               |
     - |

   * - GUI_ClearRect()
     - 为一个矩形区域填充背景颜色             |

   * - GUI_CopyRect()
     - 复制屏幕中的一个矩形区域               |

   * - GUI_DrawGradientH()
     - 绘制使用水平渐变颜色填充的矩形         |

   * - GUI_DrawGradientV()
     - 绘制使用垂直渐变颜色填充的矩形         |

   * - GUI_DrawGradientRoundedH()
     - 绘制使用水平渐变颜色填充的圆角矩形     |

   * - GUI_DrawGradientRoundedV()
     - 绘制使用垂直渐变颜色填充的圆角矩形     |

   * - GUI_DrawPixel()
     - 绘制像素点                             |

   * - GUI_DrawRect()
     - 绘制矩形                               |

   * - GUI_DrawRectEx()
     - 绘制矩形                               |

   * - GUI_DrawRoundedFrame()
     - 绘制圆角矩形框                         |

   * - GUI_DrawRoundedRect()
     - 绘制圆角矩形                           |

   * - GUI_FillRect()
     - 绘制填充颜色的矩形                     |

   * - GUI_FillRectEx()
     - 绘制填充颜色的矩形                     |

   * - GUI_FillRoundedRect()
     - 绘制填充颜色的圆角矩形                 |

   * - Alpha混合                  |
     - |

   * - GUI_EnableAlpha()
     - 开启/禁止自动Alpha混合                 |

   * - GUI_PreserveTrans()
     - 在绘制完成后保留Alpha通道              |

   * - GUI_RestoreUserAlpha()
     - 恢复Alpha混合到之前的状态              |

   * - GUI_SetAlpha()
     - 设置当前的Alpha混合值（弃用）          |

   * - GUI_SetUserAlpha()
     - 设置                                   | 一个附加值，用于计算实际的Alpha混合值  |

   * - 绘制线                     |
     - |

   * - GUI_DrawHLine()
     - 绘制水平线                             |

   * - GUI_DrawLine()
     - 绘                                     | 制从起始坐标到终点坐标的线（绝对坐标） |

   * - GUI_DrawLineRel()
     - 绘制从当前位置                         | 到由X和Y距离指定终点的直线（相对坐标） |

   * - GUI_DrawLineTo()
     - 绘制从当前位置到指定终点的直线         |

   * - GUI_DrawPolyLine()
     - 绘制折线                               |

   * - GUI_DrawVLine()
     - 绘制垂直线                             |

   * - GUI_GetLineStyle()
     - 返回当前直线绘制样式                   |

   * - GUI_MoveRel()
     - 以当前位置为参考移动直线               |

   * - GUI_MoveTo()
     - 将直线指针移动到指定位置               |

   * - GUI_SetLineStyle()
     - 设置直线绘制样式                       |

   * - 绘制多边形                 |
     - |

   * - GUI_DrawPolygon()
     - 绘制多边形的轮廓                       |

   * - GUI_EnlargePolygon()
     - 放大多边形                             |

   * - GUI_FillPolygon()
     - 绘制具有颜色填充的多边形               |

   * - GUI_MagnifyPolygon()
     - 放大多边形                             |

   * - GUI_RotatePolygon()
     - 按照指定角度旋转多边形                 |

   * - 绘制圆                     |
     - |

   * - GUI_DrawCircle()
     - 绘制圆的轮廓                           |

   * - GUI_FillCircle()
     - 绘制具有颜色填充的圆                   |

   * - 绘制椭圆                   |
     - |

   * - GUI_DrawEllipse()
     - 绘制椭圆的轮廓                         |

   * - GUI_FillEllipse()
     - 绘制具有颜色填充的椭圆                 |

   * - 绘制圆弧                   |
     - |

   * - GUI_DrawArc()
     - 绘制指定角度的圆弧                     |

   * - 绘制线图                   |
     - |

   * - GUI_DrawGraph()
     - 绘制线图                               |

   * - 绘制二维码                 |
     - |

   * - GUI_QR_Create()
     - 创建二维码位图                         |

   * - GUI_QR_Delete()
     - 删除二维码位图                         |

   * - GUI_QR_Draw()
     - 绘制二维码位图                         |

   * - GUI_QR_GetInfo()
     - 返回包含有关二维码信息的结构体         |

   * - 绘制饼图                   |
     - |

   * - GUI_DrawPie()
     - 绘制一个扇形                           |


基本绘图
~~~~

绘图API
^^^^^

GUI_SetLineStyle()
''''''''''''''''''

设置直线的绘制样式。

代码清单 10‑1 函数原型

1 U8 GUI_SetLineStyle(U8 LineStyle);

1) LinStyle：新的直线样式。

..

   可用的参数有：

   GUI_LS_SOLID：直线将绘制为视线（默认）；

   GUI_LS_DASH：直线将被绘制为虚线；

   GUI_LS_DOT：直线将被绘制为点线；

   GUI_LS_DASHDOT：线条将绘制成一个破折号和一个点交替的形式；

   GUI_LS_DASHDOTDOT：线条将绘制成破折号和双点交替的形式。

GUI_DrawLine()
''''''''''''''

从指定起点绘制一条线到当前窗口中的指定终结点(绝对坐标)。

代码清单 10‑2 函数原型

1 void GUI_DrawLine(int x0, int y0, int x1, int y1);

1) x0：起始位置的X轴坐标；

2) y0：起始位置的Y轴坐标；

3) x1：终点位置的X轴坐标；

4) y1：终点位置的Y轴坐标。

GUI_DrawRect()
''''''''''''''

在当前窗口中的指定位置绘制矩形。

代码清单 10‑3 函数原型

1 void GUI_DrawRect(int x0, int y0, int x1, int y1);

5) x0：矩形右上角的X轴坐标；

6) y0：矩形右上角的Y轴坐标；

7) x1：矩形左下角的X轴坐标；

8) y1：矩形左下角的Y轴坐标。

GUI_FillRect()
''''''''''''''

在当前窗口中的指定位置绘制带填充的矩形。

代码清单 10‑4 函数原型

1 void GUI_FillRect(int x0, int y0, int x1, int y1);

参数同上，但效果不同。

GUI_DrawRoundedFrame()
''''''''''''''''''''''

在当前窗口中的指定位置绘制圆角矩形框，可指定圆角大小和边框宽度。

代码清单 10‑5 函数原型

1 void GUI_DrawRoundedFrame(int x0, int y0, int x1, int y1, int r, int w)

2 ;

1) x0：矩形右上角的X轴坐标；

2) y0：矩形右上角的Y轴坐标；

3) x1：矩形左下角的X轴坐标；

4) y1：矩形左下角的Y轴坐标；

5) r：圆角半径；

6) w：边框宽度。

GUI_FillRoundedRect()
'''''''''''''''''''''

在当前窗口中的指定位置绘制带填充的圆角矩形。

代码清单 10‑6 函数原型

1 void GUI_FillRoundedRect(int x0, int y0, int x1, int y1, int r);

1) x0：矩形右上角的X轴坐标；

2) y0：矩形右上角的Y轴坐标；

3) x1：矩形左下角的X轴坐标；

4) y1：矩形左下角的Y轴坐标；

5) r：圆角半径。

GUI_DrawGradientV()
'''''''''''''''''''

绘制使用垂直渐变颜色填充的矩形。

代码清单 10‑7 函数原型

1 void GUI_DrawGradientV(int x0, int y0, int x1, int y1, GUI_COLOR

2 Color0, GUI_COLOR Color1);

1) x0：矩形右上角的X轴坐标；

2) y0：矩形右上角的Y轴坐标；

3) x1：矩形左下角的X轴坐标；

4) y1：矩形左下角的Y轴坐标；

5) Color0：要在矩形最顶层绘制的颜色；

6) Color1：要在矩形最底层绘制的颜色。

GUI_DrawGradientRoundedV()
''''''''''''''''''''''''''

绘制使用水平渐变颜色填充的圆角矩形。

代码清单 10‑8 函数原型

1 void GUI_DrawGradientRoundedV(int x0, int y0, int x1, int y1, int rd

2 GUI_COLOR Color0, GUI_COLOR Color1);

1) x0：矩形右上角的X轴坐标；

2) y0：矩形右上角的Y轴坐标；

3) x1：矩形左下角的X轴坐标；

4) y1：矩形左下角的Y轴坐标；

5) rd：圆角半径；

6) Color0：要在矩形最顶层绘制的颜色；

7) Color1：要在矩形最底层绘制的颜色。

基本绘图实验
^^^^^^

代码分析
''''

下面我们在模拟器上使用上述绘图API编写一段程序，看看实际效果，程序见代码清单 10‑9。

代码清单 10‑9 MainTask函数

1 void MainTask(void)

2 {

3 GUI_Init();

4

5 /\* 设置背景色 \*/

6 GUI_SetBkColor(GUI_WHITE);

7 GUI_Clear();

8 /\* 绘制破折号直线 \*/

9 GUI_SetColor(GUI_BLACK);

10 GUI_SetLineStyle(GUI_LS_DASH);

11 GUI_DrawLine(70, 10, 170, 110);

12 /\* 绘制点直线 \*/

13 GUI_SetLineStyle(GUI_LS_DOT);

14 GUI_DrawLine(50, 10, 170, 130);

15 /\* 绘制实心直线 \*/

16 GUI_SetLineStyle(GUI_LS_SOLID);

17 GUI_DrawLine(30, 10, 170, 150);

18 GUI_SetPenSize(4);

19 GUI_DrawLine(10, 10, 170, 170);

20 /\* 绘制矩形 \*/

21 GUI_SetColor(GUI_BLUE);

22 GUI_DrawRect(210, 10, 290, 90);

23 GUI_FillRect(310, 10, 390, 90);

24 /\* 绘制圆角矩形 \*/

25 GUI_SetColor(GUI_ORANGE);

26 GUI_DrawRoundedFrame(210, 110, 290, 190, 20, 8);

27 GUI_FillRoundedRect(310, 110, 390, 190, 20);

28 /\* 绘制渐变色圆角矩形 \*/

29 GUI_DrawGradientRoundedV(410, 10, 490, 190, 20, GUI_LIGHTMAGENTA,

30 GUI_LIGHTCYAN);

31

32 while (1) {

33 GUI_Delay(100);

34 }

35 }

36

基本绘图的API函数较为简单，有不明白的地方可以查阅前面的内容和官方API参考手册。这里需要注意的是，GUI_SetLineStyle()函数只有在画笔大小为1的时候才有效，画线函数的坐标x0的值必须小于x1的值，否则函数无法显示。

实验现象
''''

实验结果如图 10‑1所示，本实验只是让读者熟悉绘制API的使用，比较简单。

|Ddrawi002|

图 10‑1 实验结果

Alpha混合
~~~~~~~

Alpha混合（Alpha Blending）是一种将半透明前景色与背景色相结合产生新的混合色，从而实现透明度效果的过程。前景色的半透明度可以从完全透明到完全不透明不等。如果前景色完全透明，则新的混合色就是背景色；相反，如果它是完全不透明的，则新的混合色就是前景色。半透明度可以在这些极端值之间变化。
混合颜色由前景色和背景色以及各自的透明度通过加权平均计算得出。

.. _绘图api-1:

绘图API
^^^^^

GUI_EnableAlpha()
'''''''''''''''''

启用或禁用自动 Alpha 混合。

代码清单 10‑10 函数原型

1 unsigned GUI_EnableAlpha(unsigned OnOff);

1) OnOff：1 启用自动 Alpha 混合，0 禁用。

..

   返回值：设置前的状态。

GUI\_ SetAlpha ()
'''''''''''''''''

为所有后续绘图操作启用软件Alpha混合。

代码清单 10‑11 函数原型

1 unsigned GUI_SetAlpha(U8 Value);

1) Value：要用于所有后续绘图操作的 Alpha 值，默认为完全不透明。

..

   返回值：上一次Alpha混合的值。

注意：在标有ARGB后缀的emWin核心库中，Alpha通道值为0表示完全透明，255表示完全不透明。而无此后缀的核心库则相反，255表示完全透明，0表示完全不透明。

Alpha混合实验
^^^^^^^^^

在模拟器上编写一段程序，熟悉上述绘图API函数的使用，程序见代码清单 10‑12。

.. _代码分析-1:

代码分析
''''

代码清单 10‑12 MainTask函数

1 void MainTask(void)

2 {

3 GUI_Init();

4

5 /\* 设置背景颜色 \*/

6 GUI_SetBkColor(GUI_WHITE);

7 GUI_Clear();

8 /\* 使能自动Alpha混合 \*/

9 GUI_EnableAlpha(1);

10 /\* 将Alpha数值添加到颜色中并显示 \*/

11 GUI_SetColor(0xFF0000 \| (0xE0uL << 24));

12 GUI_FillCircle(100, 100, 50);

13 GUI_SetColor(0x00FF00 \| (0x60uL << 24));

14 GUI_FillRect(90, 90, 190, 190);

15 /\* 禁止自动Alpha混合 \*/

16 GUI_EnableAlpha(0);

17

18 while (1) {

19 GUI_Delay(100);

20 }

21 }

22

Alpha 混合完全自动执行，用户只需要调用GUI_EnableAlpha()使能 Alpha 混合，然后在设置颜色的时候添加Alpha通道值即可。32位ARGB颜色空间的最高8位用作Alpha值的设置。由于Alpha混合会增加处理器的负担，所以在使用完后一定记得禁止自动Alpha混合。需要注意一点
，如果使用emWin官方定义好的颜色宏来指定图形颜色，那么Alpha混合是无效的，只能直接输入十六进制的颜色数值才能让Alpha混合起效，具体原因目前暂不清楚。

.. _实验现象-1:

实验现象
''''

Alpha混合实验在模拟器上的运行结果如图 10‑2所示。

|Ddrawi003|

图 10‑2 实验结果

多边形和圆相关绘图
~~~~~~~~~

.. _绘图api-2:

绘图API
^^^^^

GUI_DrawPolygon()
'''''''''''''''''

在当前窗口中绘制由点列表定义的多边形轮廓。

代码清单 10‑13 函数原型

1 void GUI_DrawPolygon(const GUI_POINT \* pPoint, int NumPoints, int x,

2 int y);

1) pPoint：指向需要显示的多边形的点列表指针；

2) NumPoints：点列表中指定的点数；

3) x：多边形各点在x轴上的整体偏移量；

4) y：多边形各点在y轴上的整体偏移量。

此函数用于绘制多边形线框，线框的样式可通过GUI_SetLineStyle()函数修改，也就是说，更改直线的绘制样式可以让GUI_DrawPolygon()绘制出相应样式的多边形线框。点列表中的各点必须按顺序排列，否则出错。

GUI_FillPolygon()
'''''''''''''''''

在当前窗口中绘制由点列表定义的带填充的多边形。

代码函数原型

1 void GUI_FillPolygon(const GUI_POINT \* pPoint, int NumPoints, int x,

2 int y);

1) pPoint：指向需要显示的多边形的点列表指针；

2) NumPoints：点列表中指定的点数；

3) x：多边形各点在x轴上的整体偏移量；

4) y：多边形各点在y轴上的整体偏移量。

此函数用于绘制填充多边形，不受GUI_SetLineStyle()函数的影响。点列表中的各点必须按顺序排列，否则出错。

GUI_DrawCircle()
''''''''''''''''

在当前窗口中的指定位置绘制指定尺寸的线框圆。

代码清单 10‑14 函数原型

1 void GUI_DrawCircle(int x0, int y0, int r);

1) x0：圆心x轴坐标；

2) y0：圆心y轴坐标；

3) r：圆的半径。

GUI_FillCircle()
''''''''''''''''

在当前窗口中的指定位置绘制指定尺寸的填充圆。

代码清单 10‑15 函数原型

1 void GUI_FillCircle(int x0, int y0, int r);

参数同上，但效果不同。

GUI_DrawEllipse()
'''''''''''''''''

在当前窗口的指定位置绘制指定尺寸的线框椭圆。

代码清单 10‑16 函数原型

1 void GUI_DrawEllipse(int x0, int y0, int rx, int ry);

1) x0：圆心x轴坐标；

2) y0：圆心y轴坐标；

3) rx：x轴方向的半径；

4) ry：y轴方向的半径。

GUI_FillEllipse()
'''''''''''''''''

在当前窗口的指定位置绘制指定尺寸的填充椭圆。

代码清单 10‑17 函数原型

1 void GUI_FillEllipse(int x0, int y0, int rx, int ry);

参数同上，但效果不同。

GUI_DrawArc()
'''''''''''''

在当前窗口的指定位置绘制指定尺寸的圆弧。 圆弧是线框圆的一部分。

代码清单 10‑18 函数原型

1 void GUI_DrawArc(int xCenter, int yCenter, int rx, int ry, int a0, int

2 a1);

1) xCenter：圆弧的圆心x轴坐标；

2) yCenter：圆弧的圆心y轴坐标；

3) rx：x轴方向的半径；

4) ry：y轴方向的半径；

5) a0：起始角度；

6) a1：终止角度。

多边形和圆相关绘图实验
^^^^^^^^^^^

.. _代码分析-2:

代码分析
''''

代码清单 10‑19 MainTask函数

1 void MainTask(void)

2 {

3 GUI_Init();

4

5 /\* 设置背景色 \*/

6 GUI_SetBkColor(GUI_WHITE);

7 GUI_Clear();

8

9 /\* 绘制三角形 \*/

10 GUI_POINT TrianglePoint[] = {

11 { 0, 0 },

12 { 0, 80 },

13 { 60, 0 },

14 };

15 GUI_SetColor(GUI_RED);

16 GUI_FillPolygon(TrianglePoint, 3, 20, 20);

17 /\* 绘制虚线多边形 \*/

18 GUI_POINT PolygonPoint[] = {

19 { 30, 30 },

20 { 0, 96 },

21 { 83, 96 },

22 { 72, 6 }

23 };

24 GUI_SetColor(GUI_BLACK);

25 GUI_SetLineStyle(GUI_LS_DOT);

26 GUI_DrawPolygon(PolygonPoint, 4, 98, 15);

27 /\* 绘制正六边形 \*/

28 GUI_POINT \_aPointHexagon[] = {

29 { 0, -30 },

30 { 26, -15 },

31 { 26, 15 },

32 { 0, 30 },

33 {-26, 15 },

34 {-26, -15 },

35 };

36 GUI_SetColor(GUI_GREEN);

37 GUI_FillPolygon(&_aPointHexagon, 6, 50, 138);

38 /\* 绘制立方体正面 \*/

39 GUI_POINT SolidCube_Front[] = {

40 { 40, 140},

41 { 140, 140},

42 { 140, 40},

43 { 40, 40},

44 };

45 GUI_SetColor(0x4a51cc);

46 GUI_FillPolygon(SolidCube_Front, 4, 150, 200);

47 /\* 绘制立方体右侧 \*/

48 GUI_POINT SolidCube_RightPoint[] = {

49 { 140, 140 },

50 { 176, 104 },

51 { 176, 4 },

52 { 140, 40 },

53 };

54 GUI_SetColor(0x4d4b9d);

55 GUI_FillPolygon(SolidCube_RightPoint, 4, 150, 200);

56 /\* 绘制立方体顶部 \*/

57 GUI_POINT SolidCube_TopPoint[] = {

58 { 40, 40 },

59 { 140, 40 },

60 { 176, 4 },

61 { 76, 4 },

62 };

63 GUI_SetColor(0x585fe8);

64 GUI_FillPolygon(SolidCube_TopPoint, 4, 150, 200);

65 /\* 绘制线框圆 \*/

66 GUI_SetColor(GUI_CYAN);

67 GUI_DrawCircle(181, 111, 35);

68 /\* 绘制填充圆 \*/

69 GUI_SetColor(GUI_MAGENTA);

70 GUI_FillCircle(261, 111, 35);

71 /\* 绘制线框椭圆 \*/

72 GUI_SetColor(GUI_BLUE);

73 GUI_DrawEllipse(48, 270, 25, 50);

74 /\* 绘制填充椭圆 \*/

75 GUI_SetColor(GUI_ORANGE);

76 GUI_FillEllipse(88, 270, 60, 38);

77 while (1) {

78 GUI_Delay(100);

79 }

80 }

81

使用GUI_SetColor()函数来设置填充的颜色，调用GUI\_ FillPolygon()函数进行填充。

下面讲解一下正方体的绘制方法。不过在讲解之前，先给大家补充一种画空间几何直观图的方法：斜二测画法。

|Ddrawi004|

图 10‑3 斜二测画法示意图

斜二测画法的口诀是：平行改斜垂依旧，横等纵半竖不变。这里补充斜二测画法，主要是为了做坐标的计算。emWin没有集成3D图形库，因此如果我们需要绘制3D图形，就需要自己计算坐标。图 10‑3是一个2*2*2的正方体，由口诀的第一句话，可以知道角OBB’等于45°，第二句话说明了AA’和BB’的长度等于
原来的长度的二分之一。由此，我们就可以计算出整个正方体所有顶点的坐标值。将所得的坐标值分为三个面存放到在各自的点列表数组中，然后利用GUI_FillPolygon()函数，就可以绘制出来正方体了。

注意：emWin的默认显示坐标中，y轴的正方向是朝下的，计算正方体各面坐标时需要将其考虑在内。

如果只是绘制线框正方体，那到这一步就已经完成了。但如果是绘制带颜色填充的正方体，那么还需要给每个可见的面上色。由于光源与立方体的位置，决定了三个面颜色的不同。那如何给正方体上色呢？最简单粗暴的办法就是先用excel软件自带的形状绘制一个立方体，再利用网页工具“在线取色器”对正方体取色，就可以得到每个
面的颜色。Win10用户，可以使用画图3D工具的取色器来获取颜色值。获取的颜色值为16进值码，通过网页工具“RGB颜色值与十六进制颜色码转换工具”最终转换为RGB颜色值。

.. _实验现象-2:

实验现象
''''

最后得到的结果如图 10‑4。而且利用上面的方法画出来的立体图形的空间观感较好。

|Ddrawi005|

图 10‑4 多边形和圆相关绘图实验结果

绘制二维码
~~~~~

emWin从5.34版本开始新增了可以生成和显示二维码（QR Code）的功能，这个功能一共只有四个API函数，而且使用起来非常的方便。有了这个功能，就不需要在工程中额外包含二维码库了。有关二维码的相关知识，请参考《【野火】零死角玩转STM32—F429挑战者V2》第48章二维码识别章节。

.. _绘图api-3:

绘图API
^^^^^

GUI_QR_Create()
'''''''''''''''

创建一张二维码位图。

代码清单 10‑20 函数原型

1 GUI_HMEM GUI_QR_Create(const char \* pText, int PixelSize, int EccLevel,

2 int Version);

1) pText：需要制作成二维码的UTF-8编码的文本；

2) PixelSize：单个数据色块的大小 (以像素为单位)；

3) EccLevel：要使用的纠错编码等级，可选的纠错编码等级如下：

..

   GUI_QR_ECLEVEL_L：可以恢复7%的数据；

   GUI_QR_ECLEVEL_M：可以恢复15%的数据；

   GUI_QR_ECLEVEL_Q：可以恢复25%的数据；

   GUI_QR_ECLEVEL_H：可以恢复30%的数据。

4) Version：二维码版本号，用于规定生成的二维码的尺寸。如果设置为0（推荐），将自动计算大小。必须介于1和40之间。如果它小于给定文本与给定EccLevel所需的值，则该函数将失败。

返回值：成功时返回位图的句柄，出错时返回0。

GUI_QR_Delete()
'''''''''''''''

释放用于二维码的内存。

代码清单 10‑21 函数原型

1 void GUI_QR_Delete(GUI_HMEM hQR);

1) hQR：需要删除的二维码句柄。

如果不再使用二维码，则应将其删除，以免出现某些未知错误。

GUI_QR_Draw()
'''''''''''''

在指定的位置绘制指定的 二维 码。

代码清单 10‑22 函数原型

1 void GUI_QR_Draw(GUI_HMEM hQR, int xPos, int yPos);

1) hQR：需要显示的二维码句柄；

2) xPos：需要显示位置的x轴坐标；

3) yPos：需要显示位置的y轴坐标。

GUI_QR_GetInfo()
''''''''''''''''

返回包含有关指定 二维码代码信息的结构体。

代码清单 10‑23 函数原型

1 void GUI_QR_GetInfo(GUI_HMEM hQR, GUI_QR_INFO \* pInfo);

1) hQR：二维码句柄；

2) pInfo：指向 GUI_QR_INFO 类型的 结构体指针。

GUI_QR_INFO结构体的元素如代码清单 10‑24所示。

代码清单 10‑24 GUI_QR_INFO结构体元素

1 typedef struct {

2 int Version; // 二维码的版本号

3 int Width; // 数据色块的个数

4 int Size; //位图的大小(以像素为单位)

5 } GUI_QR_INFO;

二维码绘图实验
^^^^^^^

.. _代码分析-3:

代码分析
''''

代码清单 10‑25 MainTask函数

1 void MainTask(void)

2 {

3 GUI_HMEM hQR;

4

5 char QR_String[] = "http://www.firebbs.cn/forum.

6 php?mod=forumdisplay&fid=99";

7 GUI_Init();

8

9 /\* 设置背景色 \*/

10 GUI_SetBkColor(GUI_WHITE);

11 GUI_Clear();

12 /\* 创建二维码对象 \*/

13 hQR = GUI_QR_Create(QR_String, 5, GUI_QR_ECLEVEL_H, 0);

14 /\* 绘制二维码到LCD \*/

15 GUI_QR_Draw(hQR, 10, 10);

16 /\* 删除二维码对象 \*/

17 GUI_QR_Delete(hQR);

18 while (1) {

19 GUI_Delay(100);

20 }

21 }

22

首先创建一个二维码位图，内容是野火电子论坛emWin/ucgui专区的网址，每个数据色块的宽度为5个像素，纠错等级设置为最高，自动调整位图大小。然后将二维码绘制到LCD屏，绘制完成后删除二维码位图。

.. _实验现象-3:

实验现象
''''

在模拟器上运行上述代码，其结果如图 10‑5所示。

|Ddrawi006|

图 10‑5 二维码实验结果

2D绘图综合实验
~~~~~~~~

.. _代码分析-4:

代码分析
^^^^

(1) 多边形的点列表定义

代码清单 10‑26 多边形的点列表

1 GUI_RECT BasicRect = {10, 10, 100, 105};

2 static const unsigned aValues[] = {100, 135, 190, 240, 340, 360};

3 static const GUI_COLOR aColor[] = {GUI_BLUE, GUI_GREEN, GUI_RED,

4 GUI_CYAN, GUI_MAGENTA, GUI_YELLOW

5 };

6 static const char QR_TEXT[] = "http://www.firebbs.cn";

7 static const GUI_POINT \_aPointArrow[] = {

8 { 0, 0 },

9 {-40, -30 },

10 {-10, -20 },

11 {-10, -70 },

12 { 10, -70 },

13 { 10, -20 },

14 { 40, -30 },

15 };

16 static const GUI_POINT DashCube_BackPoint[] = {

17 { 76 , 104 },

18 { 176, 104 },

19 { 176, 4 },

20 { 76, 4 }

21 };

22 static const GUI_POINT DashCube_LeftPoint[] = {

23 { 40, 140 },

24 { 76, 104 },

25 { 76, 4 },

26 { 40, 40 }

27 };

28 static const GUI_POINT DashCube_BottonPoint[] = {

29 { 40, 140 },

30 { 140, 140 },

31 { 176, 104 },

32 { 76, 104 }

33 };

34 static const GUI_POINT DashCube_TopPoint[] = {

35 { 40, 40 },

36 { 140, 40 },

37 { 176, 4 },

38 { 76, 4 },

39 };

40 static const GUI_POINT DashCube_RightPoint[] = {

41 { 140, 140 },

42 { 176, 104 },

43 { 176, 4 },

44 { 140, 40 },

45 };

46 static const GUI_POINT DashCube_FrontPoint[] = {

47 { 40, 140},

48 { 140, 140},

49 { 140, 40},

50 { 40, 40},

51 };

以上代码的内容包括：一个矩形起始坐标和终点坐标的数组BasicRect，用于绘制饼图的角度值数组aValues和颜色数组aColor，用于生成二维码的字符串QR_TEXT，以及用于绘制正方体六个面的多边形点列表数组。GUI_RECT在文本显示章节有过介绍，现在我们来看看GUI_COLOR和GUI_P
OINT的原型，见代码清单 10‑27。

代码清单 10‑27 GUI_COLOR和GUI_POINT的原型

1 /*GUI_COLOR原型 \*/

2 typedef U32 LCD_COLOR;

3 typedef LCD_COLOR GUI_COLOR;

4

5 /*GUI_POINT原型 \*/

6 typedef struct {

7 I16P x,y;

8 } GUI_POINT;

9

从GUI_POINT的原型可以看出，多边形的点列表实际上是一个结构体数组。

(2) 饼图绘制

代码清单 10‑28 饼图绘制函数（MainTask.c）

1 /*\*

2 \* @brief 饼图绘图函数

3 \* @note 无

4 \* @param x0：饼图圆心的x坐标

5 \* y0：饼图圆心的y坐标

6 \* r：饼图半径

7 \* @retval 无

8 \*/

9 static void Pie_Chart_Drawing(int x0, int y0, int r)

10 {

11 int i, a0 = 0, a1 = 0;

12

13 for (i = 0; i < GUI_COUNTOF(aValues); i++) {

14 if (i == 0) a0 = 0;

15 else a0 = aValues[i - 1];

16 a1 = aValues[i];

17 GUI_SetColor(aColor[i]);

18 GUI_DrawPie(x0, y0, r, a0, a1, 0);

19 }

20 }

21

在Pie_Chart_Drawing函数中通过循环调用GUI_DrawPie来实现绘制饼图的效果，每次绘制的扇形的起始和终止角度由aValues数组指定，扇形的颜色由aColor数组指定。首先判断将要绘制的是否为第一个扇形，如果是的话就将第一个扇形的起始角度a0设为0，接着指定颜色开始绘制。当然读者
也可以在角度数组中把第一个元素也就是第一个扇形的起始角度定为0，这样就不用判断了。需要注意的是，emWin的圆相关API函数，角度的增加方向都是逆时针。

(3) 二维码生成

代码清单 10‑29 二维码生成函数（MainTask.c）

1 /*\*

2 \* @brief 二维码生成

3 \* @note 无

4 \* @param pText：二维码内容

5 \* PixelSize：二维码数据色块的大小，单位：像素

6 \* EccLevel：纠错编码级别

7 \* x0：二维码图像在LCD的坐标x

8 \* y0：二维码图像在LCD的坐标y

9 \* @retval 无

10 \*/

11 static void QR_Code_Drawing(const char \*pText, int PixelSize, int

12 EccLevel, int x0, int y0)

13 {

14 GUI_HMEM hQR;

15

16 /\* 创建二维码对象 \*/

17 hQR = GUI_QR_Create(pText, PixelSize, EccLevel, 0);

18 /\* 绘制二维码到LCD \*/

19 GUI_QR_Draw(hQR, x0, y0);

20 /\* 删除二维码对象 \*/

21 GUI_QR_Delete(hQR);

22 }

23

二维码生成的API函数在前面已经有过讲解，这里只是重新封装了一下。如有不明白的地方，请参考之前的内容以及《STemWin5.44参考手册》相关内容。

(4) 2D绘图

代码清单 10‑30 2D绘图函数（MainTask.c）

1 /*\*

2 \* @brief 2D绘图函数

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 /\* 用于存放多边形旋转后的点列表 \*/

8 GUI_POINT aArrowRotatedPoints[GUI_COUNTOF(_aPointArrow)];

9 static void \_2D_Graph_Drawing(void)

10 {

11 I16 aY[125] = {0};

12 int i;

13 float pi = 3.1415926L;

14 float angle = 0.0f;

15

16 /\* 绘制各种矩形 \*/

17 GUI_SetColor(GUI_GREEN);

18 GUI_DrawRectEx(&BasicRect);

19 BasicRect.x0 += 116;

20 BasicRect.x1 += 116;

21 GUI_FillRectEx(&BasicRect);

22 GUI_SetColor(GUI_RED);

23 GUI_DrawRoundedRect(240, 10, 330, 105, 10);

24 GUI_DrawRoundedFrame(352, 10, 442, 105, 10, 10);

25 GUI_FillRoundedRect(468, 10, 558, 105, 10);

26 GUI_DrawGradientRoundedH(584, 10, 674, 105, 10, GUI_LIGHTMAGENTA,

27 GUI_LIGHTCYAN);

28 GUI_DrawGradientRoundedV(700, 10, 790, 105, 10, GUI_LIGHTMAGENTA,

29 GUI_LIGHTCYAN);

30

31 /\* 绘制线条 \*/

32 GUI_SetPenSize(10);

33 GUI_SetColor(GUI_YELLOW);

34 GUI_DrawLine(10, 140, 100, 240);

35

36 /\* 绘制多边形 \*/

37 GUI_SetColor(GUI_RED);

38 GUI_FillPolygon(_aPointArrow, 7, 190, 205);

39 /\* 旋转多边形 \*/

40 angle = pi / 2;

41 GUI_RotatePolygon(aArrowRotatedPoints,

42 \_aPointArrow,

43 (sizeof(_aPointArrow) / sizeof(_aPointArrow[0])),

44 angle);

45 GUI_FillPolygon(&aArrowRotatedPoints[0], 7, 220, 250);

46

47 /\* 绘制线框正方体 \*/

48 GUI_SetPenSize(1);

49 GUI_SetColor(0x4a51cc);

50 GUI_SetLineStyle(GUI_LS_DOT);

51 GUI_DrawPolygon(DashCube_BackPoint, 4, 210, 145);

52 GUI_DrawPolygon(DashCube_LeftPoint, 4, 210, 145);

53 GUI_DrawPolygon(DashCube_BottonPoint, 4, 210, 145);

54 GUI_SetPenSize(2);

55 GUI_SetLineStyle(GUI_LS_SOLID);

56 GUI_DrawPolygon(DashCube_TopPoint, 4, 210, 145);

57 GUI_DrawPolygon(DashCube_RightPoint, 4, 210, 145);

58 GUI_DrawPolygon(DashCube_FrontPoint, 4, 210, 145);

59

60 /\* 绘制圆 \*/

61 GUI_SetColor(GUI_LIGHTMAGENTA);

62 for (i = 10; i <= 70; i += 10) {

63 GUI_DrawCircle(560, 217, i);

64 }

65 GUI_SetColor(GUI_LIGHTCYAN);

66 GUI_FillCircle(713, 217, 70);

67

68 /\* 绘制椭圆 \*/

69 GUI_SetColor(GUI_BLUE);

70 GUI_FillEllipse(80, 393, 50, 70);

71 GUI_SetPenSize(2);

72 GUI_SetColor(GUI_WHITE);

73 GUI_DrawEllipse(80, 393, 50, 10);

74

75 /\* 绘制圆弧 \*/

76 GUI_SetPenSize(10);

77 GUI_SetColor(GUI_GRAY_3F);

78 GUI_DrawArc(240, 393, 80, 80, -30, 210);

79

80 /\* 绘制折线图 \*/

81 for (i = 0; i< GUI_COUNTOF(aY); i++) {

82 aY[i] = rand() % 100;

83 }

84 GUI_SetColor(GUI_BLACK);

85 GUI_DrawGraph(aY, GUI_COUNTOF(aY), 350, 340);

86

87 /\* 绘制饼图 \*/

88 Pie_Chart_Drawing(560, 393, 60);

89

90 /\* 绘制二维码 \*/

91 QR_Code_Drawing(QR_TEXT, 5, GUI_QR_ECLEVEL_M, 650, 330);

92 }

93

以上代码中的GUI_RotatePolygon函数是用来旋转多边形坐标的，官方在2DGL_DrawPolygon.c中演示了如何旋转多边形。

例程路径如下：\ **SeggerEval_WIN32_MSVC_MinGW_GUI_V548\Sample\Tutorial**

(5) Alpha混合

代码清单 10‑31 Alpha混合函数（MainTask.c）

1 /*\*

2 \* @brief Alpha混合

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void Alpha_Blending(void)

8 {

9 /\* 显示字符 \*/

10 GUI_SetColor(GUI_BLACK);

11 GUI_SetTextMode(GUI_TM_TRANS);

12 GUI_SetFont(GUI_FONT_32B_ASCII);

13 GUI_DispStringHCenterAt("Alpha blending", 223, 203);

14

15 /\* 开启自动Alpha混合 \*/

16 GUI_EnableAlpha(1);

17 /\* 将Alpha数值添加到颜色中并显示 \*/

18 GUI_SetColor((0xC0uL << 24) \| 0xFF0000);

19 GUI_FillRect(20, 20, 235, 235);

20 GUI_SetColor((0x80uL << 24) \| 0x00FF00);

21 GUI_FillRect(110, 110, 325, 325);

22 GUI_SetColor((0x40uL << 24) \| 0x0000FF);

23 GUI_FillRect(210, 210, 425, 425);

24 /\* 关闭自动Alpha混合 \*/

25 GUI_EnableAlpha(0);

26 }

27

2D绘图和Alpha混合代码中的API函数基本都在之前的内容中讲解过，如有不清楚的可回看前面的内容，或查阅《STemWin5.44参考手册》相关内容。

.. _实验现象-4:

实验现象
^^^^

2D绘图综合实验效果如图 10‑6所示，Alpha混合效果如图 10‑7所示。

|Ddrawi007|

图 10‑6 2D绘图综合实验效果图

|Ddrawi008|

图 10‑7 Alpha混合效果图

.. |Ddrawi002| image:: media\Ddrawi002.png
   :width: 4.31132in
   :height: 1.72453in
.. |Ddrawi003| image:: media\Ddrawi003.png
   :width: 1.85849in
   :height: 1.85849in
.. |Ddrawi004| image:: media\Ddrawi004.jpg
   :width: 1.95833in
   :height: 1.14178in
.. |Ddrawi005| image:: media\Ddrawi005.png
   :width: 3.15625in
   :height: 3.15625in
.. |Ddrawi006| image:: media\Ddrawi006.png
   :width: 2.33304in
   :height: 2.33304in
.. |Ddrawi007| image:: media\Ddrawi007.png
   :width: 5.76806in
   :height: 3.46083in
.. |Ddrawi008| image:: media\Ddrawi008.png
   :width: 5.76806in
   :height: 3.46228in
