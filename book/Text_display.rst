.. vim: syntax=rst

文本显示
============

从本章开始，我们将全程以野火F429挑战者V2开发板为例，讲解如何使用emWin的一些基本功能和控件等。第8章及之后所有章节的内容均支持野火全系列开发板，使用时仅需针对开发板实际硬件做少量改动即可。

在本章节中，我们将会讲解emWin的文本显示功能。文本显示应该是LCD上用得最多的一个功能，而在emWin中显示文本也是其最基础的功能。emWin提供了很多的文本显示API函数，使用这些API函数可以实现各种文本效果。

简单文本显示
~~~~~~

在4.4 小节中的BASIC_HelloWorld工程中，其实就是一个最简单的文本显示例程，它调用了GUI_DispString()函数，并将需要显示的字符串作为参数，即可在屏幕上显示字符。

那如果我们想要只调用一个函数来显示多行字符怎么办呢？在emWin中我们任然可以使用控制字符来处理多行字符串，表格 8‑1中是emWin所支持的控制字符。

表格 8‑1 支持的控制字符表

.. list-table::
   :widths: 25 25 25 25
   :header-rows: 0


   * - 字符代码 | AS
     - II码 | C
     - | 描述
     - |

   * - 10
     - LF
     - \\n
     - 换行。                                 |

       当前                                   | 文本位置更改为下一行的开头，默认值：X  | = 0。                                  |

       Y + =字体-距离（单位：像素                 | ）（例如GUI_GetFontDistY()计算得到）。 |

   * - 13
     - CR
     - \\r
     - 回程。                                 |

       当前文本位置更改为当前行的开头。       |

       默认值：X = 0。                        |


利用好换行符\n可以方便的显示多行字符。换行符可以作为字符串的一部分，这样就可以只用一个函数显示拥有多行字符的字符串了。

文本显示API
~~~~~~~

emWin为用户提供了很多的文本显示API，如表格 8‑2所示。

表格 8‑2文本显示API函数列表

.. list-table::
   :widths: 50 50
   :header-rows: 0


   * - 函数名                       | 描
     - |

   * - 字符显示函数                 |
     - |

   * - GUI_DispCEOL()
     - 清除当前行从当前位置到结束位置       |

   * - GUI_DispChar()
     - 在当前位置显示单个字符               |

   * - GUI_DispCharAt()
     - 在指定位置显示单个字符               |

   * - GUI_DispChars()
     - 指定次数的重复显示单个字符           |

   * - GUI_DispString()
     - 在当前位置显示一个字符串             |

   * - GUI_DispStringAt()
     - 在指定位置显示一个字符串             |

   * - GUI_DispStringAtCEOL()
     - 在指定位                             | 置显示一个字符串，并清除当前行到行尾 |

   * - GUI_DispStringHCenterAt()
     - 在指定位置显示水平居中的字符串       |

   * - GUI_DispStringInRect()
     - 在指定的矩形中显示字符串             |

   * - GUI_DispStringInRectEx()
     - 在指定矩                             | 形中显示包含在矩形中的字符串，带旋转 |

   * - GUI_DispStringInRectWrap()
     - 在指定矩形中                         | 显示包含在矩形中的字符串，带换行模式 |

   * - GUI_DispStringinRectWrapEx()
     - 在指定矩形中显                       | 示包含在矩形中的字符串，带换行和旋转 |

   * - GUI_DispStringLen()
     - 在当前位置显示具有指定字符数的字符串 |

   * - GUI_WrapGetNumLines()
     - 返回在指定换行模式下字符串所需的行数 |

   * - 绘制模式                     |
     - |

   * - GUI_GetTextMode()
     - 返回当前的文本绘制模式               |

   * - GUI_SetTextMode()
     - 设置文本绘制模式                     |

   * - GUI_SetTextStyle()
     - 设置文本显示样式                     |

   * - 对齐方式                     |
     - |

   * - GUI_GetTextAlign()
     - 返回当前的文本对齐方式               |

   * - GUI_SetLBorder()
     - 设置换行后的左边框大小               |

   * - GUI_SetTextAlign()
     - 设置文本对齐方式                     |

   * - 坐标设置                     |
     - |

   * - GUI_DispNextLine()
     - 将光标移动到下一行的开头             |

   * - GUI_GotoX()
     - 设置X坐标                            |

   * - GUI_GotoXY()
     - 设置X和Y坐标                         |

   * - GUI_GotoY()
     - 设置Y坐标                            |

   * - GUI_GetDispPosX()
     - 返回当前X坐标                        |

   * - GUI_GetDispPosY()
     - 返回当前Y坐标                        |


重要函数
~~~~

GUI_DispCEOL()
^^^^^^^^^^^^^^

将当前窗口（或显示）中的当前行从当前文本位置清除到窗口末尾，行高度为当前字体的高度。

代码清单 8‑1 GUI_DispCEOL()原型

1 void GUI_DispCEOL(void);

2

GUI_DispStringInRectWrap()
^^^^^^^^^^^^^^^^^^^^^^^^^^

在当前窗口中，使用当前字体在指定矩形内的指定位置显示字符串，并可选择换行模式。

代码清单 8‑2 GUI_DispStringInRectWrap()原型

1 void GUI_DispStringInRectWrap(const char \* s,

2 GUI_RECT \* pRect,

3 int TextAlign,

4 GUI_WRAPMODE WrapMode);

1) s：想要显示的字符串

2) pRect：要写入的矩形(以像素为单位)。

3) TextAlign：对齐标志。可使用“或”操作进行组合。水平对齐标志和垂直对齐标志应该组合使用。可用的标志见表格 8‑3：

表格 8‑3 对齐标志

============== ==================
对齐标志       描述
============== ==================
水平对齐
GUI_TA_LEFT    水平左对齐（默认）
GUI_TA_HCENTER 水平垂直对齐
GUI_TA_RIGHT   水平右对齐
垂直对齐
GUI_TA_TOP     垂直顶部对齐
GUI_TA_VCENTER 垂直中心对齐
GUI_TA_BOTTOM  垂直底部对齐
============== ==================

4) WrapMode：换行模式。有三种换行模式，分别是：

..

   GUI_WRAPMODE_NONE：不执行自动换行；

   GUI_WRAPMODE_WORD：按照字对齐，对文本进行自动换行；

   GUI_WRAPMODE_CHAR：按照字符对齐，对文本进行自动换行。

GUI_SetTextMode()
^^^^^^^^^^^^^^^^^

按照指定的参数设置文本绘制模式。

代码清单 8‑3 GUI_SetTextMode()原型

1 int GUI_SetTextMode(int TextMode);

2

1) TextMode：要设置的文本模式。可以是TEXTMODE标志的任意组合。

..

   参数TextMode允许的值见表格 8‑4；

表格 8‑4 TextMode参数值

=================== ======================
TextMode参数值      描述
=================== ======================
GUI_TEXTMODE_NORMAL 正常文本显示，默认设置
GUI_TEXTMODE_REV    使文本反向显示
GUI_TEXTMODE_TRANS  使文本显示为透明
GUI_TEXTMODE_XOR    使文本反转背景
=================== ======================

这些参数值可通过“或”操作进行组合使用，例如透明反转文本就是通过参数GUI_TM_TRANS \| GUI_TM_REV设置的，此绘制模式是透明文本和反转文本的组合，与透明文本一样，它不会覆盖背景，而且和反转文本一样，该文本会反转显示。

GUI_SetTextAlign()
^^^^^^^^^^^^^^^^^^

为当前窗口中的字符串显示设置文本对齐方式。

代码清单 8‑4 GUI_SetTextAlign()原型

1 int GUI_SetTextAlign(int TextAlign);

2

1) TextAlign：要设置的文本对齐方式。允许的值如下，可使用“或”操作进行组合：

..

   水平对齐方式：GUI_TA_LEFT、GUI_TA_HCENTER、GUI_TA_RIGHT

   垂直对齐方式：GUI_TA_TOP、GUI_TA_VCENTER、GUI_TA_BOTTOM

文本显示实验
~~~~~~

代码设计
^^^^

这里只讲解核心的部分代码，部分函数的设置和头文件的包含等并没有涉及到，完整的代码请参考本章配套的工程。

编程要点
''''

1) 设置桌面背景色；

2) 设置字体背景色、字体颜色和字体大小；

3) 调用字符串显示函数，完成对字符串的显示。

每一种字符串显示都可以大致按照上述三个步骤，进行程序编写，这是最基础的框架。

代码分析
''''

(1) 初始化代码分析

我们先从main文件入手开始分析，main文件中除了操作系统的任务创建和每个任务各自的任务入口以外，还包括了板子的外设初始化函数BSP_Init()，见代码清单 8‑5。

代码清单 8‑5 BSP_Init外设初始化（main.c文件）

1 /*\*

2 \* @brief 板级外设初始化

3 \* @note 所有板子上的初始化均可放在这个函数里面

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void BSP_Init(void)

8 {

9 /\* CRC和emWin没有关系，只是他们为了库的保护而做的

10 \* 这样STemWin的库只能用在ST的芯片上面，别的芯片是无法使用的。

11 \*/

12 RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_CRC, ENABLE);

13

14 /\*

15 \* STM32中断优先级分组为4，即4bit都用来表示抢占优先级，范围为：0~15

16 \* 优先级分组只需要分组一次即可，以后如果有其他的任务需要用到中断，

17 \* 都统一用同一个优先级分组，千万不要再分组，切记。

18 \*/

19 NVIC_PriorityGroupConfig(NVIC_PriorityGroup_4);

20

21 /\* LED 初始化 \*/

22 LED_GPIO_Config();

23 /\* 串口初始化 \*/

24 Debug_USART_Config();

25 /\* 蜂鸣器初始化 \*/

26 Beep_GPIO_Config();

27 /\* 初始化触摸屏 \*/

28 GTP_Init_Panel();

29 /\* SDRAM初始化 \*/

30 SDRAM_Init();

31 /\* LCD初始化 \*/

32 LCD_Init();

33 /\* 禁用WiFi模块 \*/

34 BL8782_PDN_INIT();

35 }

36

我们看到，BSP_Init函数初始化了板子的各种外设，包括CRC时钟、NVIC、LED、串口、蜂鸣器、触摸屏、外部SDRAM、液晶触摸屏和SD卡等等。这其中最重要的就是CRC时钟初始化，想要在STM32上使用emWin就必须在GUI初始化之前开启CRC时钟，不然emWin将无法使用。但CRC时钟与e
mWin本身并没有什么联系，这只是ST为STemWin做的一个保护，这样STemWin就只能在ST的芯片上运行。

(2) GUI任务代码分析

在使用emWin之前，需要对其初始化，我们把emWin的初始化函数放到了GUI任务的开头，见代码清单 8‑6。

代码清单 8‑6 GUI_Task任务函数（main.c文件）

1 /*\*

2 \* @brief GUI任务主体

3 \* @note 无

4 \* @param 无

5 \* @retval 无

6 \*/

7 static void GUI_Task(void\* parameter)

8 {

9 /\* 初始化STemWin \*/

10 GUI_Init();

11 /\* 开启多缓冲 \*/

12 WM_MULTIBUF_Enable(1);

13

14 while (1) {

15 MainTask();

16 }

17 }

18

在代码清单 8‑6中，我们在FreeRTOS的GUI任务中调用了GUI_Init()函数和WM_MULTIBUF_Enable()函数，然后在while(1)循环中调用emWin主函数MainTask。GUI_Init()函数主要是初始化emWin内部的数据结构和变量，在使用几乎所有的emWin功能
之前都必须首先调用此函数。WM_MULTIBUF_Enable()函数是开启自动使用多帧缓冲功能，此功能可以有效的缓解控件移动或某些绘图操作产生的画面撕裂，并可在一定程度上提升系统流畅度。开启自动使用多帧缓冲后，emWin的窗口管理器会将所有绘图函数的输出重定向到不可见的后台缓冲区，然后再绘制无效的
窗口，绘制完最后一个无效窗口后，窗口管理器使后台缓冲区可见。不过此功能仅当显示驱动程序支持多个缓冲区且内存足以存储至少 2 个帧缓冲区时才可用。

(3) emWin主函数代码分析

我们在MainTask函数中展示了一些文本绘制函数的使用，见代码清单 8‑7。

代码清单 8‑7 MainTask函数（MainTask.c文件）

1 char acText[] = "This example demostrates text wrapping";

2 GUI_RECT rect = {178, 290, 310, 410};

3 GUI_WRAPMODE aWm[] = {GUI_WRAPMODE_NONE, GUI_WRAPMODE_CHAR,

4 GUI_WRAPMODE_WORD};

5

6 /*\*

7 \* @brief GUI主任务

8 \* @note 无

9 \* @param 无

10 \* @retval 无

11 \*/

12 void MainTask(void)

13 {

14 U8 i;

15

16 /\* 设置背景色 \*/

17 GUI_SetBkColor(GUI_BLUE);

18 GUI_Clear();

19

20 /\* 设置字体大小 \*/

21 GUI_SetFont(GUI_FONT_32_1);

22 GUI_DispStringAt("STemWIN@EmbeddedFire STM32F429", 10, 10);

23

24 /\* 画线 \*/

25 GUI_SetPenSize(10);

26 GUI_SetColor(GUI_RED);

27 GUI_DrawLine(272, 120, 528, 240);

28 GUI_DrawLine(272, 240, 528, 120);

29

30 /\* 绘制文本 \*/

31 GUI_SetBkColor(GUI_BLACK);

32 GUI_SetColor(GUI_WHITE);

33 GUI_SetFont(GUI_FONT_24B_ASCII);

34 /\* 正常模式 \*/

35 GUI_SetTextMode(GUI_TM_NORMAL);

36 GUI_DispStringHCenterAt("GUI_TM_NORMAL" , 400, 120);

37 /\* 反转显示 \*/

38 GUI_SetTextMode(GUI_TM_REV);

39 GUI_DispStringHCenterAt("GUI_TM_REV" , 400, 120 + 24);

40 /\* 透明文本 \*/

41 GUI_SetTextMode(GUI_TM_TRANS);

42 GUI_DispStringHCenterAt("GUI_TM_TRANS" , 400, 120 + 24 \* 2);

43 /\* 异或文本 \*/

44 GUI_SetTextMode(GUI_TM_XOR);

45 GUI_DispStringHCenterAt("GUI_TM_XOR" , 400, 120 + 24 \* 3);

46 /\* 透明反转文本 \*/

47 GUI_SetTextMode(GUI_TM_TRANS \| GUI_TM_REV);

48 GUI_DispStringHCenterAt("GUI_TM_TRANS \| GUI_TM_REV", 400, 120 + 24

49 \* 4);

50

51 /\* 在矩形区域内显示文本 \*/

52 GUI_SetFont(GUI_FONT_24B_ASCII);

53 GUI_SetTextMode(GUI_TM_TRANS);

54 for (i = 0; i < 3; i++) {

55 GUI_SetColor(GUI_WHITE);

56 GUI_FillRectEx(&rect);

57 GUI_SetColor(GUI_RED);

58 GUI_DispStringInRectWrap(acText, &rect, GUI_TA_LEFT, aWm[i]);

59 rect.x0 += 156;

60 rect.x1 += 156;

61 }

62

63 while (1) {

64 GUI_Delay(100);

65 }

66 }

67

首先定义了三个全局变量，acText用来存放将要在矩形框中显示的字符串，rect是一个存放矩形框起始坐标的结构体，aWm是一个用来存放文本对齐方式参数的枚举变量。GUI_RECT和GUI_WAPEMODE的原型如代码清单 8‑8所示。

MainTask()函数中，首先设置桌面背景颜色。这里需要注意的是，调用GUI_SetBkColor()后并不会更新桌面的背景颜色，需要在这之后调用GUI_Clear()才会更新。接着是设置字体大小，emWin内置了很多不同的字体大小，这里我们选择字体大小为32的字体。然后绘制背景图形，GUI_Se
tPenSize函数和GUI_DrawLine函数会在2D绘图章节中讲解。设置完背景颜色和图形之后，开始设置字体背景色和字体颜色，设置接下来需要显示的字体大小，然后依次显示不同绘制模式的字体，GUI_DispStringHCenterAt函数的功能是在指定位置显示水平居中的字符串，该函数的坐标参数是
字符串的居中点坐标，而不是起始坐标。在矩形框中绘制文本时，使用for循环来绘制三个不同对齐方式的矩形框文本。在while(1)中延时100毫秒，方便其他任务的运行。

代码清单 8‑8 GUI_RECT和GUI_WAPEMODE原型

1 typedef struct {

2 I16 x0,y0,x1,y1;

3 } LCD_RECT;

4 typedef LCD_RECT GUI_RECT;

5

6 typedef enum { GUI_WRAPMODE_NONE, GUI_WRAPMODE_WORD, GUI_WRAPMODE_CHAR

7 } GUI_WRAPMODE;

8

到此为止，一部分常用的文本显示API函数就讲完了。

实验现象
^^^^

文本绘制实验的实验现象如图 8‑1。可以看出不同的绘制模式和对齐方式的效果，例如异或文本会根据背景色改变局部字体颜色。

|Textdi002|

图 8‑1 文本绘制实验显示效果

.. |Textdi002| image:: media\Textdi002.png
   :width: 5.76806in
   :height: 3.46083in
